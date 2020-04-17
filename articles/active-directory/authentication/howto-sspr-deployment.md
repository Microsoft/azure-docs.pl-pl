---
title: Zagadnienia dotyczące wdrażania samoobsługowego resetowania hasła usługi Azure Active Directory
description: Dowiedz się więcej o zagadnieniach dotyczących wdrażania i strategii pomyślnego wdrożenia samoobsługowego resetowania hasła usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bec28b98a8d2640b5a8034569d49077ce6b4177
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450993"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Planowanie samoobsługowego resetowania hasła usługi Azure Active Directory

> [!IMPORTANT]
> Ten plan wdrażania zawiera wskazówki i najlepsze rozwiązania dotyczące wdrażania samoobsługowego resetowania haseł usługi Azure AD(SSPR).
>
> **Jeśli jesteś i użytkownik końcowy i musisz wrócić na [https://aka.ms/sspr](https://aka.ms/sspr)swoje konto, przejdź do **.

[Samoobsługowe resetowanie haseł (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) to funkcja usługi Azure Active Directory (AD), która umożliwia użytkownikom resetowanie haseł bez kontaktowania się z personelem IT w celu uzyskania pomocy. Użytkownicy mogą szybko odblokować się i kontynuować pracę bez względu na to, gdzie się znajdują i porę dnia. Umożliwiając pracownikom odblokowanie się, organizacja może skrócić czas nieprodukcyjny i wysokie koszty pomocy technicznej w przypadku najczęstszych problemów związanych z hasłem.

Wiele z tych funkcji ma następujące kluczowe możliwości:

* Samoobsługa umożliwia użytkownikom końcowym resetowanie wygasłych lub nieokreślonych haseł bez kontaktowania się z administratorem lub działem pomocy technicznej w celu uzyskania pomocy technicznej.
* [Funkcja Password Writeback](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) umożliwia zarządzanie hasłami lokalnymi i rozpoznawanie blokady konta w chmurze.
* Raporty aktywności zarządzania hasłami dają administratorom wgląd w resetowanie haseł i rejestrację występujących w ich organizacji.

W tym przewodniku wdrażania pokazano, jak zaplanować, a następnie przetestować wdrożenie samowzdronie samowzdronie.

Aby szybko wyświetlić wiele łajdaków SSPR w działaniu, a następnie wrócić do zrozumienia dodatkowych zagadnień dotyczących wdrażania:

> [!div class="nextstepaction"]
> [Włączanie samoobsługowego resetowania hasła (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Dowiedz się więcej o SSPR

Dowiedz się więcej o świ sspr. Zobacz [Jak to działa: samoobsługowe resetowanie hasła usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks).

### <a name="key-benefits"></a>Najważniejsze korzyści

Główne korzyści wynikające z włączenia funkcji SSPR to:

* **Zarządzanie kosztem**. Wiele łat. Zmniejsza również koszt czasu straconego z powodu utraty haseł i blokad. 

* **Intuicyjna obsługa**. Zapewnia intuicyjny jednorazowy proces rejestracji użytkownika, który pozwala użytkownikom na resetowanie haseł i odblokowywanie kont na żądanie z dowolnego urządzenia lub lokalizacji. SSPR pozwala użytkownikom wrócić do pracy szybciej i być bardziej produktywne.

* **Elastyczność i bezpieczeństwo**. Wiele łasc umożliwia przedsiębiorstwom dostęp do zabezpieczeń i elastyczności zapewnianych przez platformę chmurową. Administratorzy mogą zmieniać ustawienia, aby dostosować się do nowych wymagań dotyczących zabezpieczeń i wdrażać te zmiany dla użytkowników bez zakłócania ich logowania.

* **Solidna inspekcja i śledzenie użycia**. Organizacja może zapewnić, że systemy biznesowe pozostają bezpieczne, podczas gdy jej użytkownicy resetują własne hasła. Niezawodne dzienniki inspekcji zawierają informacje o każdym kroku procesu resetowania hasła. Te dzienniki są dostępne w interfejsie API i umożliwiają użytkownikowi importowanie danych do wybranego systemu monitorowania zdarzeń zabezpieczeń i zdarzeń (SIEM).

### <a name="licensing"></a>Licencjonowanie

Usługa Azure Active Directory jest licencjonowana dla każdego użytkownika, co oznacza, że każdy użytkownik wymaga odpowiedniej licencji dla funkcji, których używają. Zalecamy licencjonowanie oparte na grupach dla sspr. 

Aby porównać wersje i funkcje oraz włączyć licencjonowanie grupowe lub oparte na użytkownikach, zobacz [Wymagania licencyjne dotyczące resetowania hasła samoobsługowego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing).

Aby uzyskać więcej informacji na temat cen, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Wymagania wstępne

* Działająca dzierżawa usługi Azure AD z włączoną co najmniej próbną wersją licencji. W razie potrzeby [utwórz go za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto z uprawnieniami administratora globalnego.


### <a name="training-resources"></a>Zasoby szkoleniowe

| Zasoby| Łącze i opis |
| - | - |
| Filmy wideo| [Zapewnij użytkownikom lepszą skalowalność IT](https://youtu.be/g9RpRnylxS8) 
| |[Co to jest samoobsługowe resetowanie hasła?](https://youtu.be/hc97Yx5PJiM)|
| |[Wdrażanie samoobsługowego resetowania hasła](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Jak skonfigurować samoobsługowe resetowanie hasła dla użytkowników w usłudze Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Jak [przygotować użytkowników] do zarejestrowania [swoich] informacji o zabezpieczeniach usługi Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Kursy online|[Zarządzanie tożsamościami w usłudze Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Użyj funkcji SSPR, aby zapewnić użytkownikom nowoczesne, chronione środowisko. Zobacz szczególnie moduł["Zarządzanie użytkownikami i grupami usługi Azure Active Directory".](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents) |
|Pluralsight Płatne kursy |[Kwestie zarządzania tożsamością i dostępem](https://www.pluralsight.com/courses/identity-access-management-issues) Dowiedz się więcej o iam i problemów z zabezpieczeniami, które należy pamiętać w organizacji. Zobacz szczególnie moduł "Inne metody uwierzytelniania".|
| |[Wprowadzenie do pakietu Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi rozszerzania zasobów lokalnych do chmury w sposób umożliwiający uwierzytelnianie, autoryzację, szyfrowanie i bezpieczne środowisko mobilne. Zobacz szczególnie moduł "Konfigurowanie zaawansowanych funkcji usługi Microsoft Azure Active Directory Premium".
|Samouczki |[przeprowadzanie pilotażowego wdrożenia samoobsługowego resetowania hasła usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[włączanie zapisywania zwrotnego haseł](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Resetowanie hasła usługi Azure AD z ekranu logowania dla systemu Windows 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| Często zadawane pytania|[Często zadawane pytania dotyczące zarządzania hasłami](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Architektura rozwiązania

W poniższym przykładzie opisano architekturę rozwiązania resetowania hasła dla typowych środowisk hybrydowych.

![diagram architektury rozwiązań](./media/howto-sspr-deployment//solutions-architecture.png)

Opis przepływu pracy

Aby zresetować hasło, użytkownicy przechodzą do [portalu resetowania hasła](https://aka.ms/sspr). Muszą zweryfikować wcześniej zarejestrowaną metodę lub metody uwierzytelniania, aby udowodnić swoją tożsamość. Jeśli hasło pomyślnie zresetują, rozpoczną proces resetowania.

* W przypadku użytkowników korzystających z usługi Azure AD, którzy mają tylko w chmurze, przechowuje nowe hasło. 

* W przypadku użytkowników hybrydowych funkcja SSPR zapisuje hasło do usługi Active Directory on-prem za pośrednictwem usługi Azure AD Connect. 

Uwaga: Dla użytkowników, którzy mają [wyłączono synchronizację skrótów haseł (PHS),](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) sspr przechowuje hasła tylko w usłudze Active Directory.

### <a name="best-practices"></a>Najlepsze rozwiązania

Możesz pomóc użytkownikom szybko się zarejestrować, wdrażając wiele łatW wraz z inną popularną aplikacją lub usługą w organizacji. Ta akcja wygeneruje dużą ilość logowania i będzie napędzać rejestrację.

Przed wdrożeniem sspr można określić liczbę i średni koszt każdego wywołania resetowania hasła. Można użyć tego wdrożenia post danych, aby pokazać wartość, którą sspr przynosi do organizacji.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Włączanie połączonej rejestracji dla sspr i usługi MFA

Firma Microsoft zaleca, aby organizacje włączały połączone środowisko rejestracji dla samookalecowego i uwierzytelniania wieloskładnikowego. Po włączeniu tej połączonej rejestracji użytkownicy muszą tylko raz wybrać swoje informacje rejestracyjne, aby włączyć obie funkcje.

Połączone środowisko rejestracji nie wymaga od organizacji włączania zarówno samospr. Rejestracja łączona zapewnia organizacjom lepsze środowisko użytkownika. Aby uzyskać więcej informacji, zobacz [Połączone rejestracje informacji o zabezpieczeniach](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Planowanie projektu wdrożeniowego

Należy wziąć pod uwagę potrzeby organizacyjne podczas określania strategii dla tego wdrożenia w twoim środowisku.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich interesariuszy

Gdy projekty technologiczne nie powiodą się, zazwyczaj robią to z powodu niedopasowanych oczekiwań dotyczących wpływu, wyników i odpowiedzialności. Aby uniknąć tych pułapek, [upewnij się, że angażujesz odpowiednie zainteresowane strony](https://aka.ms/deploymentplans) i że role interesariuszy w projekcie są dobrze zrozumiałe, dokumentując zainteresowane strony oraz ich wkład i odpowiedzialność w projekcie.

#### <a name="required-administrator-roles"></a>Wymagane role administratora


| Rola biznesowa/persona| Rola usługi Azure AD (jeśli to konieczne) |
| - | - |
| Dział pomocy technicznej poziomu 1| Administrator haseł |
| Dział pomocy technicznej poziomu 2| Administrator użytkownika |
| Administrator SSPR| Administrator globalny |


### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja ma kluczowe znaczenie dla powodzenia każdej nowej usługi. Należy proaktywnie komunikować się z użytkownikami, jak zmieni się ich środowisko, kiedy to się zmieni i jak uzyskać wsparcie, jeśli wystąpią problemy. Zapoznaj się z [materiałami do wdrażania wdrażania samoobsługowego resetowania haseł w centrum pobierania firmy Microsoft,](https://www.microsoft.com/download/details.aspx?id=56768) aby uzyskać informacje na temat planowania strategii komunikacji z użytkownikiem końcowym.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Zaleca się, że początkowa konfiguracja sspr jest w środowisku testowym. Rozpocznij od grupy pilotażowej, włączając wiele numer SSPR dla podzbioru użytkowników w organizacji. Zobacz [Najważniejsze wskazówki dotyczące programu pilotażowego](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans).

Aby utworzyć grupę, zobacz, jak [utworzyć grupę i dodać członków usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal). 

## <a name="plan-configuration"></a>Konfiguracja planu

Następujące ustawienia są wymagane, aby włączyć wiele SSPR wraz z zalecanymi wartościami.

| Obszar | Ustawienie | Wartość |
| --- | --- | --- |
| **Właściwości SSPR** | Samoobsługowe resetowanie hasła włączone | **Wybrana** grupa dla pilota / **Wszystko** do produkcji |
| **Metody uwierzytelniania** | Metody uwierzytelniania wymagane do rejestracji | Zawsze 1 więcej niż jest to wymagane do zresetowania |
|   | Metody uwierzytelniania wymagane do zresetowania | Jeden lub dwa |
| **Rejestracja** | Czy wymagać od użytkowników rejestrowania się podczas logowania? | Tak |
|   | Liczba dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania | 90 – 180 dni |
| **Powiadomienia** | Czy powiadamiać użytkowników o resetowaniu hasła? | Tak |
|   | Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło? | Tak |
| **Dostosowywanie** | Dostosowywanie łącza do pomocy technicznej | Tak |
|   | Niestandardowa poczta e-mail lub adres URL pomocy technicznej | Witryna pomocy technicznej lub adres e-mail |
| **Integracja lokalna** | Odpisy haseł do lokalnej usługi AD | Tak |
|   | Zezwalaj użytkownikom na odblokowywanie konta bez resetowania hasła | Tak |

### <a name="sspr-properties"></a>Właściwości SSPR

Podczas włączania funkcji SSPR wybierz odpowiednią grupę zabezpieczeń w środowisku pilotażowym.

* Aby wymusić rejestrację SSPR dla wszystkich, zalecamy użycie opcji **Wszystkie.**
* W przeciwnym razie wybierz odpowiednią grupę zabezpieczeń usługi Azure AD lub AD.

### <a name="authentication-methods"></a>Metody uwierzytelniania

Gdy funkcja SSPR jest włączona, użytkownicy mogą zresetować swoje hasło tylko wtedy, gdy mają dane obecne w metodach uwierzytelniania włączonych przez administratora. Metody obejmują telefon, powiadomienie aplikacji Authenticator, pytania zabezpieczające itp. Aby uzyskać więcej informacji, zobacz [Co to są metody uwierzytelniania?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods).

Zalecamy następujące ustawienia metody uwierzytelniania:

* Ustaw **metody uwierzytelniania wymagane do zarejestrowania się** na co najmniej jeden więcej niż liczba wymagana do zresetowania. Zezwalanie na wielokrotne uwierzytelnianie zapewnia użytkownikom elastyczność, gdy trzeba je zresetować.

* Ustaw **liczbę metod wymaganych do zresetowania** do poziomu odpowiedniego dla twojej organizacji. Jeden wymaga najmniejszego tarcia, podczas gdy dwa mogą zwiększyć postawę bezpieczeństwa. 

Uwaga: Użytkownik musi mieć skonfigurowane metody uwierzytelniania w [zasadach haseł i ograniczeniach w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

### <a name="registration-settings"></a>Ustawienia rejestracji

Ustaw **wymagaj od użytkowników rejestrowania się podczas logowania** się do **tak**. To ustawienie wymaga, aby użytkownicy rejestrowali się podczas logowania, zapewniając ochronę wszystkim użytkownikom.

Ustaw **liczbę dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie informacji uwierzytelniających** na okres od **90** do **180** dni, chyba że organizacja potrzebuje krótszego przedziału czasowego.

### <a name="notifications-settings"></a>Ustawienia powiadomień

Skonfiguruj zarówno **opcję Powiadom użytkowników o resetowania hasła,** jak i **powiadamiaj wszystkich administratorów, gdy inni administratorzy resetują hasło** do **Tak**. Wybranie **opcji Tak** w obu zwiększa bezpieczeństwo, zapewniając, że użytkownicy są świadomi, kiedy ich hasło jest resetowane. Zapewnia również, że wszyscy administratorzy są świadomi, gdy administrator zmienia hasło. Jeśli użytkownicy lub administratorzy otrzymają powiadomienie i nie zainicjowali zmiany, mogą natychmiast zgłosić potencjalny problem z zabezpieczeniami.

### <a name="customization-settings"></a>Ustawienia dostosowywania

Bardzo ważne jest, aby dostosować adres e-mail lub adres URL działu pomocy technicznej, aby użytkownicy, którzy napokonują problemy, mogli natychmiast uzyskać pomoc. Ustaw tę opcję na wspólny adres e-mail pomocy technicznej lub stronę sieci Web, którą użytkownicy znają. 

Aby uzyskać więcej informacji, zobacz [Dostosowywanie funkcji usługi Azure AD w celu samodzielnego resetowania hasła](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization).

### <a name="password-writeback"></a>Odpisanie hasła

**Funkcja Zapisywanie hasła** jest włączona za pomocą [usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) i zapisuje resetowanie haseł w chmurze z powrotem do istniejącego katalogu lokalnego w czasie rzeczywistym. Aby uzyskać więcej informacji, zobacz [Co to jest zapisywanie hasła?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

Zalecamy następujące ustawienia:

* Upewnij się, że **hasła dokonywać zapisu do lokalnej usługi AD** są ustawione na **Tak**. 
* Ustaw **opcję Zezwalaj użytkownikom na odblokowywanie konta bez resetowania hasła** na **Tak**.

Domyślnie usługa Azure AD odblokowuje konta podczas resetowania hasła.

### <a name="administrator-password-setting"></a>Ustawienie hasła administratora

Konta administratorów mają podwyższone uprawnienia. Lokalni administratorzy przedsiębiorstwa lub domeny nie mogą zresetować swoich haseł za pomocą samookapowania. Lokalne konta administracyjne mają następujące ograniczenia:

* hasło można zmienić tylko w środowisku on-prem.
* nigdy nie może używać tajnych pytań i odpowiedzi jako metody resetowania hasła.

Zaleca się, aby nie synchronizować kont administratorów usługi Active Directory w przedsprzedaży z usługą Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Środowiska z wieloma systemami zarządzania tożsamościami

Niektóre środowiska mają wiele systemów zarządzania tożsamościami. Lokalni menedżerowie tożsamości, tacy jak Oracle AM i SiteMinder, wymagają synchronizacji z usługą AD dla haseł. Można to zrobić za pomocą narzędzia, takiego jak usługa powiadamiania o zmianie hasła (PCNS) z programem Microsoft Identity Manager (MIM). Aby znaleźć informacje na temat tego bardziej złożonego scenariusza, zobacz artykuł [Wdrażanie usługi powiadamiania o zmianie hasła mim na kontrolerze domeny](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Planowanie testowania i pomocy technicznej

Na każdym etapie wdrażania od początkowych grup pilotażowych do całej organizacji, upewnij się, że wyniki są zgodnie z oczekiwaniami.

### <a name="plan-testing"></a>Planowanie testowania

Aby upewnić się, że wdrożenie działa zgodnie z oczekiwaniami, zaplanuj zestaw przypadków testowych, aby sprawdzić poprawność implementacji. Aby ocenić przypadki testowe, potrzebujesz użytkownika testowego niebędącego administratorem z hasłem. Jeśli chcesz utworzyć użytkownika, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

Poniższa tabela zawiera przydatne scenariusze testów, których można użyć do dokumentowania oczekiwanych wyników organizacji na podstawie zasad.
<br>


| Sprawa biznesowa| Oczekiwane wyniki |
| - | - |
| Portal SSPR jest dostępny z poziomu sieci firmowej| Określana przez organizację |
| Portal SSPR jest dostępny spoza sieci firmowej| Określana przez organizację |
| Resetowanie hasła użytkownika z przeglądarki, gdy użytkownik nie jest włączony do resetowania hasła| Użytkownik nie może uzyskać dostępu do przepływu resetowania hasła |
| Resetowanie hasła użytkownika z przeglądarki, gdy użytkownik nie zarejestrował się do resetowania hasła| Użytkownik nie może uzyskać dostępu do przepływu resetowania hasła |
| Użytkownik loguje się po wymuszaniu rejestracji resetowania hasła| Monituje użytkownika o zarejestrowanie informacji zabezpieczających |
| Użytkownik loguje się po zakończeniu rejestracji resetowania hasła| Monituje użytkownika o zarejestrowanie informacji zabezpieczających |
| Portal SSPR jest dostępny, gdy użytkownik nie ma licencji| Jest dostępny |
| Resetowanie hasła użytkownika z ekranu blokady urządzenia przyłączone do usługi Azure AD systemu Windows 10 lub hybrydowej usługi Azure AD| Użytkownik może zresetować hasło |
| Dane rejestracyjne i użytkowe SSPR są dostępne dla administratorów w czasie zbliżonym do rzeczywistego| Jest dostępny za pośrednictwem dzienników inspekcji |

Można również zapoznać się [z opcję Ukończ samoobsługową rolkę pilotażową resetowania haseł usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) W tym samouczku włączysz wdrożenie pilotażowe samoobjawy w organizacji i przetestujesz przy użyciu konta niebędącego administratorem.

### <a name="plan-support"></a>Obsługa planu

Podczas gdy samoo wiele samose nie tworzy zazwyczaj problemów użytkownika, ważne jest, aby przygotować personel pomocy technicznej do radzenia sobie z problemami, które mogą wystąpić. Podczas gdy administrator może zresetować hasło dla użytkowników końcowych za pośrednictwem portalu usługi Azure AD, lepiej jest pomóc rozwiązać problem za pośrednictwem samoobsługowego procesu pomocy technicznej.

Aby umożliwić zespołowi pomocy technicznej sukces, możesz utworzyć często zadawane pytania na podstawie pytań otrzymywanych od użytkowników. Oto kilka przykładów:

| Scenariusze| Opis |
| - | - |
| Użytkownik nie ma żadnych zarejestrowanych metod uwierzytelniania| Użytkownik próbuje zresetować swoje hasło, ale nie ma żadnej z metod uwierzytelniania, które zarejestrował dostępne (Przykład: zostawili swój telefon komórkowy w domu i nie mogą uzyskać dostępu do poczty e-mail) |
| Użytkownik nie otrzymuje sms-a ani połączenia w biurze lub telefonie komórkowym| Użytkownik próbuje zweryfikować swoją tożsamość za pomocą wiadomości tekstowych lub połączeń, ale nie odbiera wiadomości TEKSTOWEJ/połączenia. |
| Użytkownik nie może uzyskać dostępu do portalu resetowania hasła| Użytkownik chce zresetować swoje hasło, ale nie jest włączony do resetowania hasła i nie może uzyskać dostępu do strony, aby zaktualizować hasła. |
| Użytkownik nie może ustawić nowego hasła| Użytkownik kończy weryfikację podczas resetowania hasła, ale nie może ustawić nowego hasła. |
| Użytkownik nie widzi łącza Resetowanie hasła na urządzeniu z systemem Windows 10| Użytkownik próbuje zresetować hasło z ekranu blokady systemu Windows 10, ale urządzenie nie jest połączone z usługą Azure AD lub zasady urządzenia usługi Intune nie są włączone |

### <a name="plan-rollback"></a>Planowanie wycofywania

Aby wycofać wdrożenie:

* dla pojedynczego użytkownika, usuń użytkownika z grupy zabezpieczeń 

* dla grupy, usuń grupę z konfiguracji SSPR

* Dla wszystkich wyłącz wiele komunikatów SSPR dla dzierżawy usługi Azure AD

## <a name="deploy-sspr"></a>Wdrażanie funkcji samoobsługowego resetowania haseł

Przed wdrożeniem upewnij się, że wykonaliśmy następujące czynności:

1. Utworzono i rozpoczęto realizację [planu komunikacji](#plan-communications).

1. Określono odpowiednie [ustawienia konfiguracji](#plan-configuration).

1. Zidentyfikowano użytkowników i grupy środowisk [pilotażowych](#plan-a-pilot) i produkcyjnych.

1. [Określone ustawienia konfiguracji](#plan-configuration) rejestracji i samoobsługi.

1. [Skonfigurowane zapisywanie zwrotne hasła,](#password-writeback) jeśli masz środowisko hybrydowe.


**Teraz możesz wdrożyć wiele komunikatów SSPR!**

Zobacz [Włączanie samoobsługowego resetowania hasła, aby](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) uzyskać pełne wskazówki krok po kroku dotyczące konfigurowania następujących obszarów.

1. [Metody uwierzytelniania](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Ustawienia rejestracji](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Ustawienia powiadomień](#notifications-settings)

1. [Ustawienia dostosowywania](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Integracja lokalna](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Włączanie funkcji SSPR w systemie Windows
W przypadku komputerów z systemami Windows 7, 8, 8.1 i 10 można [umożliwić użytkownikom resetowanie hasła na ekranie logowania systemu Windows](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>Zarządzanie sspr

Usługa Azure AD może dostarczyć dodatkowych informacji na temat wydajności sspr za pośrednictwem inspekcji i raportów.

### <a name="password-management-activity-reports"></a>Raporty aktywności w zakresie zarządzania hasłami 

Można użyć wstępnie utworzonych raportów w witrynie Azure portal do pomiaru wydajności samookapła. Jeśli masz odpowiednią licencję, możesz również tworzyć zapytania niestandardowe. Aby uzyskać więcej informacji, zobacz [Opcje raportowania zarządzania hasłami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  Musisz być [administratorem globalnym](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)i musisz wyrazić zgodę na zbieranie tych danych dla twojej organizacji. Aby wyrazić zgodę, należy odwiedzić kartę Raportowanie lub dzienniki inspekcji w witrynie Azure Portal co najmniej raz. Do tego czasu dane nie zbierają się dla Twojej organizacji.

Dzienniki inspekcji rejestracji i resetowania hasła są dostępne przez 30 dni. Jeśli inspekcja zabezpieczeń w firmie wymaga dłuższego przechowywania, dzienniki muszą zostać wyeksportowane i zużyte do narzędzia SIEM, takiego jak [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory), Splunk lub ArcSight.

![Zrzut ekranu przedstawiający raportowanie sspr](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Metody uwierzytelniania — użycie i szczegółowe informacje

[Użycie i szczegółowe informacje](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) pozwalają zrozumieć, jak metody uwierzytelniania dla funkcji, takich jak usługi Azure MFA i sspr działają w organizacji. Ta funkcja raportowania zapewnia organizacji środki, aby zrozumieć, jakie metody zarejestrować i jak z nich korzystać.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

* Zapoznaj się [z tematem resetowania hasła samoobsługowego](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* Postępuj zgodnie [z często zadawanymi pytaniami dotyczącymi zarządzania hasłami](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Pomocna dokumentacja

* [Co to są metody uwierzytelniania?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Jak to działa: samoobsługowe resetowanie hasła usługi Azure AD?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Dostosowywanie funkcji usługi Azure AD do samodzielnego resetowania hasła](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Password policies and restrictions in Azure Active Directory (Zasady i ograniczenia dotyczące haseł w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [Co to jest zapisywanie haseł?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć wdrażanie samoobsługowego resetowania haseł, zobacz [Włączanie samoobsługowego resetowania haseł usługi Azure AD](tutorial-enable-sspr.md)

* [Rozważ wdrożenie ochrony hasłem usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Rozważ wdrożenie inteligentnej blokady usługi Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)
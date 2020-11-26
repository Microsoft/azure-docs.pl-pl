---
title: Zagadnienia dotyczące wdrażania Azure Active Directory samoobsługowego resetowania hasła
description: Poznaj zagadnienia dotyczące wdrażania i strategię pomyślnej implementacji resetowania hasła w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 765bfe0f805ae4219110d689e8f7f8fd54a26cf3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173940"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Planowanie Azure Active Directory samoobsługowego resetowania hasła

> [!IMPORTANT]
> Ten plan wdrożenia zawiera wskazówki i najlepsze rozwiązania dotyczące wdrażania funkcji samoobsługowego resetowania haseł w usłudze Azure AD (SSPR).
>
> **Jeśli jesteś użytkownikiem końcowym i chcesz wrócić do swojego konta, przejdź do [https://aka.ms/sspr](https://aka.ms/sspr)** strony.

[Samoobsługowe resetowanie hasła (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) to funkcja Azure Active Directory (AD), która umożliwia użytkownikom resetowanie haseł bez kontaktowania się z pracownikami działu IT w celu uzyskania pomocy. Użytkownicy mogą szybko odblokować siebie i kontynuować pracę niezależnie od tego, gdzie się znajdują. Dzięki umożliwieniu pracownikom odblokowania, organizacja może skrócić czas nieproduktywny i wysokie koszty obsługi typowych problemów związanych z hasłami.

SSPR ma następujące kluczowe możliwości:

* Samoobsługowe umożliwia użytkownikom końcowym Resetowanie wygasłych lub niewygasłych haseł bez konieczności kontaktowania się z administratorem lub pomocą techniczną w celu uzyskania pomocy technicznej.
* Funkcję [zapisywania zwrotnego haseł](./concept-sspr-writeback.md) umożliwia zarządzanie hasłami lokalnymi i rozpoznawaniem blokady konta, chociaż w chmurze.
* Raporty dotyczące działań związanych z zarządzaniem hasłami umożliwiają administratorom wgląd w działanie resetowania hasła i rejestracji w organizacji.

W tym przewodniku wdrażania przedstawiono sposób planowania, a następnie testowania SSPR.

Aby szybko zobaczyć SSPR w działaniu, a następnie wrócić do zrozumienia dodatkowych zagadnień związanych z wdrażaniem:

> [!div class="nextstepaction"]
> [Włącz Samoobsługowe resetowanie hasła (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Więcej informacji na temat SSPR

Dowiedz się więcej o SSPR. Zobacz [, jak to działa: Samoobsługowe resetowanie hasła w usłudze Azure AD](./concept-sspr-howitworks.md).

### <a name="key-benefits"></a>Najważniejsze korzyści

Najważniejsze zalety włączenia SSPR są następujące:

* **Zarządzaj kosztami**. SSPR obniża koszty pomocy technicznej dzięki umożliwieniu użytkownikom resetowania haseł. Zmniejsza to także koszt utraty czasu z powodu utraconych haseł i blokad. 

* **Intuicyjne środowisko użytkownika**. Zapewnia intuicyjny proces rejestracji użytkowników, który umożliwia użytkownikom resetowanie haseł i odblokowywanie kont na żądanie z dowolnego urządzenia lub lokalizacji. SSPR umożliwia użytkownikom szybsze i wydajniejsze wykonywanie zadań.

* **Elastyczność i zabezpieczenia**. SSPR umożliwia przedsiębiorstwom dostęp do zabezpieczeń i elastyczności zapewnianej przez platformę chmury. Administratorzy mogą zmienić ustawienia, aby uwzględnić nowe wymagania dotyczące zabezpieczeń i przerzucić te zmiany użytkownikom bez zakłócania ich logowania.

* **Niezawodne inspekcje i śledzenie użycia**. Organizacja może zagwarantować, że systemy biznesowe pozostaną bezpieczne, a użytkownicy zresetują własne hasła. Niezawodne dzienniki inspekcji zawierają informacje o każdym kroku procesu resetowania hasła. Te dzienniki są dostępne z interfejsu API i umożliwiają użytkownikowi Importowanie danych do wybranego systemu zdarzeń zabezpieczeń i monitorowania zdarzenia (SIEM).

### <a name="licensing"></a>Licencjonowanie

Azure Active Directory jest licencjonowany dla poszczególnych użytkowników, co oznacza, że każdy użytkownik wymaga odpowiedniej licencji dla funkcji, których używają. Zalecamy korzystanie z licencjonowania opartego na grupach dla SSPR. 

Aby porównać wersje i funkcje oraz włączyć Licencjonowanie na podstawie grup lub użytkowników, zobacz [wymagania dotyczące licencjonowania dla funkcji samoobsługowego resetowania hasła w usłudze Azure AD](./concept-sspr-licensing.md).

Aby uzyskać więcej informacji na temat cen, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Wymagania wstępne

* Działająca dzierżawa usługi Azure AD z włączoną co najmniej próbną wersją licencji. W razie potrzeby [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto z uprawnieniami administratora globalnego.


### <a name="training-resources"></a>Zasoby szkoleniowe

| Zasoby| Link i opis |
| - | - |
| Filmy wideo| [ZAPEWNIAnie użytkownikom lepszej skalowalności](https://youtu.be/g9RpRnylxS8) 
| |[Co to jest samoobsługowe resetowanie hasła?](https://youtu.be/hc97Yx5PJiM)|
| |[Wdrażanie samoobsługowego resetowania hasła](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Jak skonfigurować Samoobsługowe resetowanie haseł dla użytkowników w usłudze Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Instrukcje [przygotowanie użytkowników do] Register [ich] informacje o zabezpieczeniach dla Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Kursy online|[Zarządzanie tożsamościami w Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Użyj SSPR, aby nadać użytkownikom nowoczesne, chronione środowisko pracy. Zobacz szczególnie "[zarządzanie Azure Active Directory użytkownikami i grupami](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)". |
|Kursy płatne Pluralsight |[Problemy związane z zarządzaniem tożsamościami i dostępem](https://www.pluralsight.com/courses/identity-access-management-issues) Dowiedz się więcej o problemach z usługą IAM i zabezpieczeniami, które należy znać w organizacji. Zobacz szczególnie "inne metody uwierzytelniania".|
| |[Wprowadzenie z pakietem Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi rozszerzania zasobów lokalnych do chmury w sposób umożliwiający uwierzytelnianie, autoryzację, szyfrowanie i bezpieczne środowisko mobilne. Zobacz szczególnie "Konfigurowanie zaawansowanych funkcji Microsoft Azure Active Directory — wersja Premium".
|Samouczki |[przeprowadzanie pilotażowego wdrożenia samoobsługowego resetowania hasła usługi Azure AD](./tutorial-enable-sspr.md) |
| |[włączanie zapisywania zwrotnego haseł](./tutorial-enable-sspr-writeback.md) |
| |[Resetowanie hasła usługi Azure AD na ekranie logowania dla systemu Windows 10](./howto-sspr-windows.md) |
| Często zadawane pytania|[Często zadawane pytania dotyczące zarządzania hasłami](./active-directory-passwords-faq.md) |


### <a name="solution-architecture"></a>Architektura rozwiązania

W poniższym przykładzie opisano architekturę rozwiązania resetowania hasła dla typowych środowisk hybrydowych.

![diagram architektury rozwiązania](./media/howto-sspr-deployment//solutions-architecture.png)

Opis przepływu pracy

Aby zresetować hasło, użytkownicy przejdą do [portalu resetowania haseł](https://aka.ms/sspr). Muszą zweryfikować wcześniej zazarejestrowaną metodę lub metody uwierzytelniania, aby potwierdzić swoją tożsamość. Po pomyślnym zresetowaniu hasła rozpocznie się proces resetowania.

* W przypadku użytkowników tylko w chmurze SSPR zapisuje nowe hasło w usłudze Azure AD. 

* W przypadku użytkowników hybrydowych SSPR zapisuje hasło w Active Directory Premium za pośrednictwem usługi Azure AD Connect. 

Uwaga: w przypadku użytkowników, którzy mają wyłączone polecenie [synchronizacji skrótów haseł (PHS)](../hybrid/whatis-phs.md) , SSPR zapisuje hasła wyłącznie w Active Directory na Premium.

### <a name="best-practices"></a>Najlepsze rozwiązania

Możesz pomóc użytkownikom szybko rejestrować się, wdrażając SSPR wraz z inną popularną aplikacją lub usługą w organizacji. Ta akcja spowoduje wygenerowanie dużej ilości logowań i umożliwi rejestrację dysku.

Przed wdrożeniem SSPR możesz określić liczbę i średni koszt każdego wywołania resetowania hasła. To wdrożenie służy do wyświetlania wartości SSPR w organizacji.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Włącz rejestrację połączoną dla SSPR i usługi MFA

Firma Microsoft zaleca, aby organizacje umożliwiały łączenie się ze sobą przy użyciu usługi SSPR i uwierzytelniania wieloskładnikowego. Po włączeniu tego połączonego środowiska rejestracji użytkownicy będą musieli tylko wybrać informacje o rejestracji, aby włączyć obie funkcje.

Połączone środowisko rejestracji nie wymaga, aby organizacje umożliwiały zarówno SSPR, jak i usługę Azure AD Multi-Factor Authentication. Połączona Rejestracja zapewnia organizacjom lepszy komfort pracy użytkowników. Aby uzyskać więcej informacji, zobacz artykuł [łączenie informacji o zabezpieczeniach](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Planowanie projektu wdrożenia

Podczas określania strategii tego wdrożenia w danym środowisku należy wziąć pod uwagę potrzeby organizacyjne.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

Gdy projekty technologii kończą się niepowodzeniem, zazwyczaj są to spowodowane niezgodnością oczekiwań, rezultatów i obowiązków. Aby uniknąć tych pułapek, [należy się upewnić, że angażuje się w odpowiednie osoby zainteresowane](../fundamentals/active-directory-deployment-plans.md) i że role udziałowców w projekcie są zrozumiałe w celu udokumentowania uczestników projektu i ich danych wejściowych i accountabilities.

#### <a name="required-administrator-roles"></a>Wymagane role administratorów


| Rola biznesowa/osoba| Rola usługi Azure AD (w razie potrzeby) |
| - | - |
| Pomoc techniczna na poziomie 1| Administrator haseł |
| Pomoc techniczna poziomu 2| Administrator użytkowników |
| SSPR administrator| Administrator globalny |


### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Należy aktywnie komunikować się z użytkownikami, w jaki sposób ich środowisko zostanie zmienione, gdy ulegnie zmianie, oraz jak uzyskać pomoc techniczną, jeśli wystąpią problemy. Zapoznaj się z [materiałami wdrożeniowymi samoobsługowego resetowania hasła w centrum pobierania Microsoft,](https://www.microsoft.com/download/details.aspx?id=56768) aby zapoznać się z pomysłami dotyczącymi planowania strategii komunikacji użytkowników końcowych.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Zalecamy, aby początkowa konfiguracja SSPR była w środowisku testowym. Zacznij od grupy pilotażowej, włączając SSPR dla podzbioru użytkowników w organizacji. Zapoznaj [się z najlepszymi rozwiązaniami dla pilotażu](../fundamentals/active-directory-deployment-plans.md).

Aby utworzyć grupę, zobacz jak [utworzyć grupę i dodać członków w Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md). 

## <a name="plan-configuration"></a>Konfiguracja planu

Następujące ustawienia są wymagane do włączenia SSPR oraz zalecanych wartości.

| Obszar | Ustawienie | Wartość |
| --- | --- | --- |
| **Właściwości SSPR** | Włączono Samoobsługowe resetowanie hasła | **Wybrana** Grupa dla pilotażu/ **wszystko** dla środowiska produkcyjnego |
| **Metody uwierzytelniania** | Metody uwierzytelniania wymagane do zarejestrowania | Zawsze 1 więcej niż wymagane do zresetowania |
|   | Metody uwierzytelniania wymagane do zresetowania | Jeden lub dwa |
| **Rejestracja** | Czy wymagać od użytkowników rejestrowania się podczas logowania? | Tak |
|   | Liczba dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania | 90 – 180 dni |
| **Powiadomienia** | Czy powiadamiać użytkowników o resetowaniu hasła? | Tak |
|   | Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło? | Tak |
| **Dostosowywanie** | Dostosuj link do pomocy technicznej | Tak |
|   | Niestandardowa wiadomość e-mail lub adres URL pomocy technicznej | Witryna lub adres e-mail pomocy technicznej |
| **Integracja lokalna** | Zapisuj hasła do lokalnej usługi AD | Tak |
|   | Zezwalaj użytkownikom na Odblokowywanie konta bez resetowania hasła | Tak |

### <a name="sspr-properties"></a>Właściwości SSPR

Podczas włączania SSPR wybierz odpowiednią grupę zabezpieczeń w środowisku pilotażowym.

* Aby wymusić rejestrację SSPR dla wszystkich, zalecamy użycie opcji **All** .
* W przeciwnym razie wybierz odpowiednią grupę zabezpieczeń usługi Azure AD lub AD.

### <a name="authentication-methods"></a>Metody uwierzytelniania

Gdy SSPR jest włączona, użytkownicy mogą resetować swoje hasła tylko wtedy, gdy mają dane obecne w metodach uwierzytelniania włączonych przez administratora. Metody obejmują telefon, powiadomienie aplikacji uwierzytelniania, pytania zabezpieczające itd. Aby uzyskać więcej informacji, zobacz [co to są metody uwierzytelniania?](./concept-authentication-methods.md).

Zalecamy następujące ustawienia metody uwierzytelniania:

* Ustaw **metody uwierzytelniania wymagane do zarejestrowania** na co najmniej jednej wartości wymaganej do zresetowania. Umożliwienie wielu uwierzytelnień zapewnia elastyczność użytkownikom, gdy wymagają zresetowania.

* Ustaw **liczbę metod wymaganych do zresetowania** do poziomu właściwego dla Twojej organizacji. Jeden z nich wymaga najmniejszego tarcia, a dwa mogą zwiększyć stan zabezpieczeń. 

Uwaga: użytkownik musi dysponować metodami uwierzytelniania skonfigurowanymi w [zasadach haseł i ograniczeniach w Azure Active Directory](./concept-sspr-policy.md).

### <a name="registration-settings"></a>Ustawienia rejestracji

Ustaw opcję **Wymagaj, aby użytkownicy rejestrowali się podczas logowania** do programu **tak**. To ustawienie wymaga, aby użytkownicy rejestrowali się podczas logowania, co gwarantuje, że wszyscy użytkownicy są chronieni.

Ustaw **liczbę dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania** do zakresu od **90** do **180** dni, chyba że Twoja organizacja ma potrzebę biznesową w krótszym czasie.

### <a name="notifications-settings"></a>Ustawienia powiadomień

Skonfiguruj zarówno opcję **Powiadamiaj użytkowników o** resetowaniu hasła, jak i **Powiadom wszystkich administratorów, gdy inni administratorzy zresetują swoje hasła** na **wartość tak**. Wybranie opcji **tak** powoduje zwiększenie bezpieczeństwa przez zapewnienie użytkownikom informacji o resetowaniu hasła. Zapewnia również, że wszyscy administratorzy są świadomi, gdy administrator zmieni hasło. Jeśli użytkownicy lub Administratorzy otrzymają powiadomienie i nie zainicjowano zmiany, mogą natychmiast zgłosić potencjalny problem z zabezpieczeniami.

### <a name="customization-settings"></a>Ustawienia dostosowania

W celu zapewnienia użytkownikom, którzy napotykają problemy, można uzyskać pomoc natychmiastowo. Ustaw tę opcję na wspólny adres e-mail pomocy technicznej lub stronę internetową, z którą znają użytkownicy. 

Aby uzyskać więcej informacji, zobacz [Dostosowywanie funkcji usługi Azure AD do samoobsługowego resetowania hasła](./howto-sspr-customization.md).

### <a name="password-writeback"></a>Zapisywanie zwrotne haseł

Funkcja **zapisywania zwrotnego haseł** jest włączona z [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) i zapisuje resetowanie haseł w chmurze z powrotem do istniejącego katalogu lokalnego w czasie rzeczywistym. Aby uzyskać więcej informacji, zobacz [co to jest zapisywanie zwrotne haseł?](./concept-sspr-writeback.md)

Zalecamy następujące ustawienia:

* Upewnij się, że **zapis z powrotem do lokalnej usługi AD** ma ustawioną **wartość tak**. 
* Ustaw opcję **zezwól użytkownikom na Odblokowywanie konta bez resetowania hasła** na **wartość tak**.

Domyślnie usługa Azure AD odblokowuje konta, gdy wykonuje Resetowanie hasła.

### <a name="administrator-password-setting"></a>Ustawienie hasła administratora

Konta administratorów mają podwyższony poziom uprawnień. Administratorzy lokalnego przedsiębiorstwa lub domeny nie mogą zresetować swoich haseł za pomocą SSPR. Lokalne konta administratorów mają następujące ograniczenia:

* można zmienić hasło tylko w środowisku Premium.
* nie można nigdy używać tajnych pytań i odpowiedzi jako metody resetowania hasła.

Zalecamy, aby nie synchronizować kont administratorów Premium Active Directory z usługą Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Środowiska z wieloma systemami zarządzania tożsamościami

Niektóre środowiska mają wiele systemów zarządzania tożsamościami. Lokalne Menedżery tożsamości, takie jak Oracle AM i SiteMinder, wymagają synchronizacji z usługą AD dla haseł. Można to zrobić przy użyciu narzędzia, takiego jak Usługa powiadamiania o zmianie hasła (PCNS) z Microsoft Identity Manager (MIM). Aby uzyskać informacje na temat bardziej złożonej scenariusza, zobacz artykuł [wdrażanie usługi powiadamiania o zmianie hasła w programie MIM na kontrolerze domeny](/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Planowanie testowania i pomocy technicznej

Na każdym etapie wdrożenia od początkowych grup pilotażowych za pośrednictwem całej organizacji upewnij się, że wyniki są zgodnie z oczekiwaniami.

### <a name="plan-testing"></a>Testowanie planu

Aby upewnić się, że wdrożenie działa zgodnie z oczekiwaniami, Zaplanuj zestaw przypadków testowych, aby sprawdzić poprawność implementacji. Aby ocenić przypadki testowe, potrzebny jest Użytkownik testowy niebędący administratorem przy użyciu hasła. Jeśli musisz utworzyć użytkownika, zobacz [Dodawanie nowych użytkowników do Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).

Poniższa tabela zawiera przydatne scenariusze testowania, których można użyć do udokumentowania oczekiwanych wyników organizacji na podstawie zasad.
<br>


| Przypadek biznesowy| Oczekiwane wyniki |
| - | - |
| Portal SSPR jest dostępny z poziomu sieci firmowej| Określone przez organizację |
| Portal SSPR jest dostępny spoza sieci firmowej| Określone przez organizację |
| Zresetuj hasło użytkownika z przeglądarki, gdy użytkownik nie ma włączonej obsługi resetowania hasła| Użytkownik nie może uzyskać dostępu do przepływu resetowania hasła |
| Zresetuj hasło użytkownika z przeglądarki, gdy użytkownik nie jest zarejestrowany do resetowania hasła| Użytkownik nie może uzyskać dostępu do przepływu resetowania hasła |
| Użytkownik loguje się w przypadku wymuszenia rejestracji resetowania hasła| Monituj użytkownika o zarejestrowanie informacji o zabezpieczeniach |
| Użytkownik loguje się po zakończeniu rejestracji resetowania hasła| Monituj użytkownika o zarejestrowanie informacji o zabezpieczeniach |
| Portal SSPR jest dostępny, gdy użytkownik nie ma licencji| Jest dostępny |
| Resetowanie hasła użytkownika z usługi Azure AD przyłączonej do systemu Windows 10 lub hybrydowego ekranu blokady urządzenia dołączonego do usługi Azure AD| Użytkownik może zresetować hasło |
| SSPR dane rejestracji i użycia są dostępne dla administratorów w czasie niemal rzeczywistym| Jest dostępny za pośrednictwem dzienników inspekcji |

Możesz również zajrzeć do usługi Azure AD samoobsługowego [resetowania hasła](./tutorial-enable-sspr.md). W tym samouczku zostanie włączone wdrożenie pilotażowe SSPR w organizacji i przetestowanie go przy użyciu konta innego niż administrator.

### <a name="plan-support"></a>Planowanie pomocy technicznej

Chociaż SSPR nie tworzy zwykle problemów z użytkownikami, ważne jest, aby przygotować personel pomocy technicznej do rozwiązywania problemów, które mogą wystąpić. Administrator może zresetować hasło dla użytkowników końcowych za pośrednictwem portalu usługi Azure AD, dlatego lepiej jest rozwiązać ten problem za pośrednictwem samoobsługowego procesu obsługi.

Aby umożliwić powodzenie zespołu pomocy technicznej, możesz utworzyć często zadawane pytania na podstawie pytań otrzymywanych od użytkowników. Oto kilka przykładów:

| Scenariusze| Opis |
| - | - |
| Użytkownik nie ma dostępnych żadnych zarejestrowanych metod uwierzytelniania| Użytkownik próbuje zresetować swoje hasło, ale nie ma żadnej z metod uwierzytelniania, które zostały zarejestrowane (przykład: pozostawiły swój telefon komórkowy w domu i nie może uzyskać dostępu do poczty e-mail) |
| Użytkownik nie otrzymuje tekstu ani nie dzwoni na telefon biurowy lub komórkowy| Użytkownik próbuje zweryfikować swoją tożsamość za pośrednictwem tekstu lub wywołania, ale nie otrzymuje tekstu/wywołania. |
| Użytkownik nie może uzyskać dostępu do portalu resetowania haseł| Użytkownik chce zresetować swoje hasło, ale nie ma włączonej możliwości resetowania hasła i nie może uzyskać dostępu do strony w celu zaktualizowania haseł. |
| Użytkownik nie może ustawić nowego hasła| Użytkownik kończy weryfikację podczas przepływu resetowania hasła, ale nie może ustawić nowego hasła. |
| Użytkownik nie widzi linku resetowania hasła na urządzeniu z systemem Windows 10| Użytkownik próbuje zresetować hasło na ekranie blokady systemu Windows 10, ale urządzenie nie jest przyłączone do usługi Azure AD lub nie jest włączone zasady dotyczące urządzeń w usłudze Intune |

### <a name="plan-rollback"></a>Planowanie wycofywania

Aby wycofać wdrożenie:

* dla pojedynczego użytkownika Usuń użytkownika z grupy zabezpieczeń 

* w przypadku grupy Usuń grupę z konfiguracji SSPR

* Dla wszystkich użytkowników Wyłącz SSPR dla dzierżawy usługi Azure AD

## <a name="deploy-sspr"></a>Wdrażanie funkcji samoobsługowego resetowania haseł

Przed wdrożeniem upewnij się, że wykonano następujące czynności:

1. Utworzono i rozpoczęto wykonywanie [planu komunikacji](#plan-communications).

1. Określono odpowiednie [Ustawienia konfiguracji](#plan-configuration).

1. Zidentyfikowano użytkowników i grupy dla środowiska [pilotażowego](#plan-a-pilot) i produkcyjnego.

1. [Określono ustawienia konfiguracji](#plan-configuration) rejestracji i samoobsługi.

1. [Skonfigurowane zapisywanie zwrotne haseł](#password-writeback) w przypadku środowiska hybrydowego.


**Teraz możesz przystąpić do wdrażania SSPR.**

Zobacz Włącz samoobsługowe [resetowanie haseł](./tutorial-enable-sspr.md#enable-self-service-password-reset) , aby uzyskać pełne instrukcje krok po kroku dotyczące konfigurowania następujących obszarów.

1. [Metody uwierzytelniania](./concept-authentication-methods.md)

1. [Ustawienia rejestracji](./concept-registration-mfa-sspr-combined.md)

1. [Ustawienia powiadomień](#notifications-settings)

1. [Ustawienia dostosowania](./howto-sspr-customization.md)

1. [Integracja lokalna](./tutorial-enable-sspr-writeback.md)

### <a name="enable-sspr-in-windows"></a>Włącz SSPR w systemie Windows
W przypadku maszyn z systemem Windows 7, 8, 8,1 i 10 można [umożliwić użytkownikom Resetowanie hasła na ekranie logowania systemu Windows](./howto-sspr-windows.md)

## <a name="manage-sspr"></a>Zarządzanie SSPR

Usługa Azure AD może zapewnić dodatkowe informacje na temat wydajności SSPR za pomocą inspekcji i raportów.

### <a name="password-management-activity-reports"></a>Raporty dotyczące działań związanych z zarządzaniem hasłami 

Do mierzenia wydajności SSPR można użyć wstępnie utworzonych raportów na Azure Portal. Jeśli masz odpowiednie licencje, możesz również utworzyć niestandardowe zapytania. Aby uzyskać więcej informacji, zobacz [Opcje raportowania dotyczące zarządzania hasłami w usłudze Azure AD](./howto-sspr-reporting.md)

> [!NOTE]
>  Musisz być [administratorem globalnym](../roles/permissions-reference.md)i musisz wybrać, aby te dane były zbierane dla organizacji. Aby zrezygnować z programu, należy odwiedzić kartę Raportowanie lub dzienniki inspekcji w witrynie Azure Portal co najmniej raz. Do tego momentu dane nie są zbierane dla Twojej organizacji.

Dzienniki inspekcji na potrzeby rejestracji i resetowania haseł są dostępne przez 30 dni. Jeśli Inspekcja zabezpieczeń w ramach firmy wymaga dłuższego przechowywania, dzienniki muszą zostać wyeksportowane i zużyte do narzędzia SIEM, takiego jak [Azure](../../sentinel/connect-azure-active-directory.md), Splunk lub ArcSight.

![Zrzut ekranu raportowania SSPR](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Metody uwierzytelniania — użycie i szczegółowe informacje

[Użycie i szczegółowe informacje](./howto-authentication-methods-usage-insights.md) pozwalają zrozumieć, jak metody uwierzytelniania dla funkcji, takich jak usługa Azure AD MFA i SSPR, działają w organizacji. Ta funkcja raportowania umożliwia organizacji korzystanie z metod, które umożliwiają zapoznanie się z metodami rejestrowania i korzystania z nich.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

* Zobacz [Rozwiązywanie problemów z samoobsługowym resetowaniem hasła](./troubleshoot-sspr.md) 

* Śledź [często zadawane pytania dotyczące zarządzania hasłami](./active-directory-passwords-faq.md) 

### <a name="helpful-documentation"></a>Przydatna dokumentacja

* [Co to są metody uwierzytelniania?](./concept-authentication-methods.md)

* [Jak to działa: Samoobsługowe resetowanie haseł w usłudze Azure AD?](./concept-sspr-howitworks.md)

* [Dostosowywanie funkcji usługi Azure AD do samoobsługowego resetowania hasła](./howto-sspr-customization.md)

* [Password policies and restrictions in Azure Active Directory (Zasady i ograniczenia dotyczące haseł w usłudze Azure Active Directory)](./concept-sspr-policy.md)

* [Co to jest zapisywanie zwrotne haseł?](./concept-sspr-writeback.md)

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć wdrażanie SSPR, zobacz Włącz samoobsługowe [resetowanie haseł w usłudze Azure AD](tutorial-enable-sspr.md)

* [Rozważ zaimplementowanie ochrony hasłem usługi Azure AD](./concept-password-ban-bad.md)

* [Rozważ zaimplementowanie inteligentnej blokady usługi Azure AD](./howto-password-smart-lockout.md)
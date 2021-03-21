---
title: Utwórz plan zabezpieczeń dla dostępu zewnętrznego do Azure Active Directory
description: Zaplanuj zabezpieczenia dostępu zewnętrznego do zasobów organizacji.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266142240ba9e892c905ac8aa6521da5a14c4c3d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554024"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. Utwórz plan zabezpieczeń dla dostępu zewnętrznego 

Teraz, po [ustaleniu żądanego dostępu do usługi Security stan Security stan w celu](1-secure-access-posture.md) [odnalezienia bieżącego stanu współpracy](2-secure-access-current-state.md), można utworzyć plan zabezpieczenia i zarządzanie użytkownikami zewnętrznymi. 

Ten plan powinien udokumentować następujące elementy:

* Aplikacje i inne zasoby, które powinny być pogrupowane w celu uzyskania dostępu.

* Odpowiednie warunki logowania dla użytkowników zewnętrznych. Mogą dotyczyć stanu urządzenia, lokalizacji logowania, wymagań aplikacji klienta i ryzyka użytkownika.

* Zasady biznesowe dotyczące przeglądania i usuwania dostępu. 

* Populacje użytkowników, które mają być pogrupowane i traktowane podobnie.

Po udokumentowaniu tych obszarów można zaimplementować ten plan za pomocą zasad zarządzania tożsamościami i dostępem z firmy Microsoft lub dowolnego innego dostawcy tożsamości (dostawcy tożsamości).

## <a name="document-resources-to-be-grouped-for-access"></a>Udokumentowanie zasobów do grupowania w celu uzyskania dostępu

Istnieje wiele sposobów grupowania zasobów na potrzeby dostępu. 

* Zespoły Microsoft Teams grupuje pliki, wątki konwersacji i inne zasoby w jednym miejscu. Należy sformułować zewnętrzną strategię dostępu dla zespołów Microsoft Teams. Zobacz [bezpieczny dostęp do zespołów, usługi OneDrive i programu SharePoint](9-secure-access-teams-sharepoint.md).

* Pakiety dostępu do zarządzania prawami umożliwiają tworzenie pojedynczego pakietu aplikacji i innych zasobów, do których można udzielić dostępu. 

* Zasady dostępu warunkowego można stosować do 250 aplikacji z takimi samymi wymaganiami dotyczącymi dostępu.

Jednak użytkownik będzie zarządzał dostępem, należy udokumentować, które aplikacje powinny być zgrupowane razem. Zagadnienia powinny obejmować:

* **Profil ryzyka**. Jakie jest ryzyko dla Twojej firmy, jeśli zły aktor uzyska dostęp do aplikacji? Należy rozważyć kodowanie każdej aplikacji jako wysokiego, średniego lub niskiego ryzyka. Należy zachować ostrożność w zakresie grupowania aplikacji wysokiego ryzyka z niskimi ryzykiem. 

   * Aplikacje dokumentów, które nigdy nie powinny być udostępniane użytkownikom zewnętrznym.

* **Struktury zgodności**. Co w przypadku, gdy wszystkie struktury zgodności muszą być zgodne z aplikacją? Jakie są wymagania dotyczące dostępu i przeglądu?

* **Aplikacje dla określonych ról lub działów zadań**. Czy istnieją aplikacje, które powinny być zgrupowane, ponieważ wszyscy użytkownicy należący do określonej roli lub działu będą musieli uzyskać dostęp?

* **Aplikacje ukierunkowane na współpracę**. Jakie aplikacje ukierunkowane na współpracę mają być dostępne dla użytkowników zewnętrznych? Może być konieczne uzyskanie dostępu do programu Microsoft Teams i programu SharePoint. W przypadku aplikacji biurowych w pakiecie Office 365, takich jak Word i Excel, użytkownicy zewnętrzni będą korzystać z własnych licencji lub będą musieli uzyskać licencję i zapewnić dostęp?

Dla każdej grupy aplikacji i zasobów, które mają być dostępne dla użytkowników zewnętrznych, należy udokumentować następujące elementy:

* Opisowa nazwa grupy, na przykład *High_Risk_External_Access_Finance*. 

* Pełna lista wszystkich aplikacji i zasobów w grupie.

* Właściciele aplikacji i zasobów oraz informacje kontaktowe.

* Czy dostęp jest kontrolowany przez niego, czy delegowany do właściciela firmy.

* Wszystkie wymagania wstępne, na przykład ukończenie sprawdzania w tle lub szkoleń, w celu uzyskania dostępu.

* Wszelkie wymagania dotyczące zgodności w celu uzyskania dostępu do zasobów.

* Wszelkie dodatkowe wyzwania, na przykład wymaganie uwierzytelniania wieloskładnikowego dla określonych zasobów.

* Jak często dostęp zostanie sprawdzony, przez kogo i gdzie będzie udokumentowany.

Ten typ planu nadzoru można także również ukończyć dla dostępu wewnętrznego.

## <a name="document-sign-in-conditions-for-external-users"></a>Warunki logowania do dokumentu dla użytkowników zewnętrznych.

W ramach planu należy określić wymagania dotyczące logowania dla użytkowników zewnętrznych, gdy uzyskują oni dostęp do zasobów. Wymagania dotyczące logowania są często oparte na profilu ryzyka zasobów i ocenie ryzyka związanego z logowaniem użytkowników.

Warunki logowania są konfigurowane w [dostępie warunkowym usługi Azure AD](../conditional-access/overview.md) i składają się z warunków i wyników. Na przykład kiedy należy wymagać uwierzytelniania wieloskładnikowego

**Warunki logowania oparte na ryzyku zasobów.**

| Profil ryzyka aplikacji| Należy wziąć pod uwagę te zasady wyzwalające uwierzytelnianie wieloskładnikowe |
| - |-|
| Niskie ryzyko| Wymagaj uwierzytelniania wieloskładnikowego dla określonych zestawów aplikacji |
| Podwyższone ryzyko| Wymagaj uwierzytelniania wieloskładnikowego, gdy obecne są inne ryzyka |
| Wysokie ryzyko| Wymagaj zawsze uwierzytelniania wieloskładnikowego dla użytkowników zewnętrznych |


Obecnie można [wymusić uwierzytelnianie wieloskładnikowe dla użytkowników B2B w dzierżawie](../external-identities/b2b-tutorial-require-mfa.md). 

**Warunki logowania użytkownika i urządzenia**.

| Ryzyko związane z użytkownikiem lub logowaniem| Weź pod uwagę te zasady |
| - | - |
| Urządzenie| Wymaganie zgodnych urządzeń |
| Aplikacji mobilnych| Wymagaj zatwierdzonych aplikacji |
| Ochrona tożsamości pokazuje wysokie ryzyko| Wymagaj od użytkownika zmiany hasła |
| Lokalizacja sieciowa| Wymagaj logowania z określonego zakresu adresów IP do wysoce poufnych projektów |

Obecnie aby można było korzystać z stanu urządzenia jako dane wejściowe dla zasad, urządzenie musi być zarejestrowane lub dołączone do dzierżawy. 

Można używać [zasad opartych na ryzyku ochrony tożsamości](../conditional-access/howto-conditional-access-policy-risk.md) . Problemy muszą jednak zostać skorygowane w dzierżawie głównej użytkownika.

W przypadku [lokalizacji sieciowych](../conditional-access/howto-conditional-access-policy-location.md)można ograniczyć dostęp do dowolnych zakresów adresów IP. Można jej użyć, jeśli chcesz, aby partnerzy zewnętrzni uzyskujący dostęp do aplikacji, gdy znajdują się w lokacji w organizacji.

[Dowiedz się więcej o zasadach dostępu warunkowego](../conditional-access/overview.md).

## <a name="document-access-review-policies"></a>Zasady przeglądu dostępu do dokumentów

Zapoznaj się z zasadami biznesowymi, gdy musisz przejrzeć dostęp do zasobów, a jeśli chcesz usunąć dostęp do konta dla użytkowników zewnętrznych. Dane wejściowe tych decyzji mogą obejmować:

* Wymagania szczegółowe dotyczące wszelkich platform zgodności.

* Wewnętrzne zasady i procesy biznesowe

* Zachowanie użytkownika

Mimo że zasady zostaną odpowiednio dostosowane do Twoich potrzeb, należy rozważyć następujące kwestie:

* **Przeglądy dostępu do zarządzania prawami**. Korzystanie z funkcji zarządzania prawami do

   * [Automatyczne wygasanie pakietów dostępu](../governance/entitlement-management-access-package-lifecycle-policy.md), a tym samym dostęp użytkowników zewnętrznych do uwzględnionych zasobów.

   * Ustaw [wymaganą częstotliwość przeglądu](../governance/entitlement-management-access-reviews-create.md) dla przeglądów dostępu.

   * Jeśli używasz [połączonych organizacji](../governance/entitlement-management-organization.md) do grupowania wszystkich użytkowników z jednego partnera, Zaplanuj regularne przeglądy przy użyciu właściciela firmy i przedstawiciela partnera.

* **Grupy Microsoft 365**. Ustaw [zasady wygasania grupy](/microsoft-365/solutions/microsoft-365-groups-expiration-policy) dla grup Microsoft 365, do których są zapraszani użytkownicy zewnętrzni. 

* **Inne opcje**. Jeśli użytkownicy zewnętrzni mają dostęp poza pakietami dostępu do zarządzania prawami lub grupami Microsoft 365, skonfiguruj proces biznesowy, aby sprawdzić, kiedy konta powinny być nieaktywne lub usunięte. Na przykład:

   * Usuń możliwość logowania dla dowolnego konta, które nie jest zalogowane do 90 dni.

   * Oceń potrzeby dostępu i podejmuj działania na końcu każdego projektu z użytkownikami zewnętrznymi.

 

## <a name="determine-your-access-control-methods"></a>Określanie metod kontroli dostępu

Teraz, gdy wiesz, jak chcesz kontrolować dostęp, jak te zasoby powinny być pogrupowane pod kątem wspólnego dostępu oraz wymaganych zasad logowania i przeglądania dostępu, możesz zdecydować, jak wykonać swój plan. 

Niektóre funkcje, na przykład [Zarządzanie uprawnieniami](../governance/entitlement-management-overview.md), są dostępne tylko z licencjami na Azure AD — wersja Premium 2 (P2). Licencje na Microsoft 365 E5 i pakiet Office 365 E5 obejmują licencje usługi Azure AD P2. 

Inne kombinacje Microsoft 365, pakietu Office 365 i usługi Azure AD umożliwiają również obsługę niektórych funkcji zarządzania użytkownikami zewnętrznymi. Aby uzyskać więcej informacji, zobacz [Information Protection](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) .

> [!NOTE]
> Licencje są na użytkownika. W związku z tym można mieć określonych użytkowników, w tym administratorów i właścicieli firm delegowanych kontroli dostępu, na poziomie P2 usługi Azure AD lub Microsoft 365 E5 bez włączania tych licencji dla wszystkich użytkowników. Pierwszy 50 000 użytkowników zewnętrznych jest bezpłatny. Jeśli nie włączysz licencji P2 dla innych użytkowników wewnętrznych, nie będą oni mogli korzystać z funkcji zarządzania prawami, takich jak pakiety dostępu. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Zarządzanie dostępem za pomocą usługi Azure AD P2 i Microsoft/Office 365 E5
Usługa Azure AD P2 i Microsoft 365 E5 mają pełen pakiet narzędzi zabezpieczeń i zarządzania.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>Inicjowanie obsługi administracyjnej, logowanie, przeglądanie dostępu i cofanie aprowizacji. Pogrubione wpisy są preferowanymi metodami

| Cecha| Udostępnianie użytkownikom zewnętrznym| Wymuś dotyczących logowania.| Przegląd dostępu| Cofanie aprowizacji dostępu |
| - | - | - | - | - |
| Współpraca B2B w usłudze Azure AD| Zapraszanie za pośrednictwem poczty e-mail, OTP, samoobsługowy| | **Okresowe przeglądy na partnera**| Usuń konto<br>Ogranicz logowanie |
| Zarządzanie prawami| **Dodawanie użytkownika za pośrednictwem przypisywania lub samoobsługowego dostępu**| | Przeglądy dostępu|**Wygaśnięcie lub usunięcie z pakietu, pakiet dostępu**|
| Grupy usługi Office 365| | | Przeglądanie członkostw w grupie| Wygaśnięcie lub usunięcie grupy<br> Grupa formularzy usuwania |
| Grupy zabezpieczeń usługi Azure AD| | **Zasady dostępu warunkowego** (w razie potrzeby Dodaj użytkowników zewnętrznych do grup zabezpieczeń)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>Dostęp do zasobów. Pogrubione wpisy są preferowanymi metodami

|Cecha | Dostęp do zasobów & aplikacji| Program SharePoint & dostęp do usługi OneDrive| Dostęp do zespołów| Bezpieczeństwo dokumentu & e-mail |
| - |-|-|-|-|
| Zarządzanie prawami| **Dodawanie użytkownika za pośrednictwem przypisywania lub samoobsługowego dostępu**| **Pakiety dostępu**| **Pakiety dostępu**|  |
| Grupa pakietu Office 365| | Dostęp do witryn (i powiązanej zawartości) dołączonych do grupy| Dostęp do zespołów (i powiązanej zawartości) dołączonych do grupy|  |
| Etykiety poufności| | **Ręczne i automatyczne klasyfikowanie i ograniczanie dostępu**| **Ręczne i automatyczne klasyfikowanie i ograniczanie dostępu**| **Ręczne i automatyczne klasyfikowanie i ograniczanie dostępu** |
| Grupy zabezpieczeń usługi Azure AD| **Zasady dostępu warunkowego dla dostępu nie są uwzględnione w pakietach dostępu**| | |  |


### <a name="entitlement-management"></a>Zarządzanie prawami 

[Pakiety dostępu do zarządzania prawami](../governance/entitlement-management-access-package-create.md) umożliwiają udostępnianie i cofanie aprowizacji dostępu do grup i zespołów, aplikacji i witryn programu SharePoint. Można zdefiniować, które połączone organizacje mają dostęp, czy żądania samoobsługi są dozwolone i jakie przepływy pracy zatwierdzania są wymagane (jeśli istnieją), aby udzielić dostępu. Aby zapewnić, że dostęp nie pozostanie dłużej niż jest to konieczne, możesz zdefiniować zasady wygasania i przeglądy dostępu dla każdego pakietu dostępu. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Zarządzanie dostępem za pomocą usługi Azure AD P1 i Microsoft/Office 365 E3
Możesz uzyskać niezawodne zarządzanie przy użyciu usługi Azure AD P1 i Microsoft 365 E3

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>Inicjowanie obsługi administracyjnej, logowanie, przeglądanie dostępu i cofanie aprowizacji


|Cecha | Udostępnianie użytkownikom zewnętrznym| Wymuś wymagania dotyczące logowania| Przegląd dostępu| Cofanie aprowizacji dostępu |
| - |-|-|-|-|
| Współpraca B2B w usłudze Azure AD| **Zapraszanie za pośrednictwem poczty e-mail, OTP, samoobsługowy**| Bezpośrednia Federacja B2B| **Okresowe przeglądy na partnera**| Usuń konto<br>Ogranicz logowanie |
| Grupy Microsoft lub Office 365| | | | Wygaśnięcie lub usunięcie grupy.<br>Usuwanie z grupy. |
| Grupy zabezpieczeń| | **Dodawanie użytkowników zewnętrznych do grup zabezpieczeń (organizacja, zespół, projekt itp.)**| |  |
| Zasady dostępu warunkowego| | **Zasady dostępu warunkowego logowania dla użytkowników zewnętrznych**| |  |


 ### <a name="access-to-resources"></a>Dostęp do zasobów.

|Cecha | Dostęp do zasobów & aplikacji| Program SharePoint & dostęp do usługi OneDrive| Dostęp do zespołów| Bezpieczeństwo dokumentu & e-mail |
| - |-|-|-|-|
| Grupy Microsoft lub Office 365| | **Dostęp do witryn dołączonych do grupy (i skojarzonej zawartości)**|**Dostęp do zespołów dołączonych do grupy Microsoft 365 (i powiązanej zawartości)**|  |
| Etykiety poufności| | Ręczne klasyfikowanie i ograniczanie dostępu| Ręczne klasyfikowanie i ograniczanie dostępu.| Ręczne klasyfikowanie w celu ograniczenia i szyfrowania |
| Zasady dostępu warunkowego| Zasady dostępu warunkowego dotyczące kontroli dostępu| | |  |
| Dodatkowe metody| | Ogranicz dostęp do witryny programu SharePoint w sposób szczegółowy przy użyciu grup zabezpieczeń.<br>Nie Zezwalaj na bezpośrednie udostępnianie.| **Ogranicz zaproszenia zewnętrzne z poziomu zespołów**|  |


### <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania zewnętrznego dostępu do zasobów. Zalecamy podejmowanie akcji w kolejności na liście.

1. [Określanie stan zabezpieczeń dla dostępu zewnętrznego](1-secure-access-posture.md)

2. [Odkryj bieżący stan](2-secure-access-current-state.md)

3. [Utwórz plan ładu](3-secure-access-plan.md) (Jesteś tutaj).

4. [Używanie grup do zabezpieczeń](4-secure-access-groups.md)

5. [Przejście do usługi Azure AD B2B](5-secure-access-b2b.md)

6. [Bezpieczny dostęp z zarządzaniem prawami](6-secure-access-entitlement-managment.md)

7. [Bezpieczny dostęp przy użyciu zasad dostępu warunkowego](7-secure-access-conditional-access.md)

8. [Bezpieczny dostęp z etykietami czułości](8-secure-access-sensitivity-labels.md)

9. [Bezpieczny dostęp do usług Microsoft Teams, OneDrive i SharePoint](9-secure-access-teams-sharepoint.md)
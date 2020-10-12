---
title: Funkcje zabezpieczeń zarządzania i monitorowania — Microsoft Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie funkcji zabezpieczeń i usług oferowanych przez platformę Azure w celu ułatwienia zarządzania i monitorowania usług Azure Cloud Services i Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: c5525fc43f23ecd1760c5a869d8171aee0e9415d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91395774"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Omówienie zarządzania i monitorowania zabezpieczeń platformy Azure
Ten artykuł zawiera omówienie funkcji zabezpieczeń i usług oferowanych przez platformę Azure w celu ułatwienia zarządzania i monitorowania usług Azure Cloud Services i Virtual Machines.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Role-Based Access Control (RBAC) zapewnia szczegółowe zarządzanie dostępem do zasobów platformy Azure. Za pomocą RBAC, można przyznać użytkownikom tylko dostęp do ich zadań. Kontrola RBAC może również pomóc w zapewnieniu, że osoby, które opuściją organizację, utracą dostęp do zasobów w chmurze.

Więcej informacji:

* [Blog zespołu Active Directory w sprawie RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem

Na platformie Azure możesz używać oprogramowania chroniącego przed złośliwym kodem od głównych dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro, McAfee i Kaspersky pomogą. To oprogramowanie pomaga chronić maszyny wirtualne przed złośliwymi plikami, programami reklamujące i innymi zagrożeniami.

Program Microsoft chroniący przed złośliwym kodem dla platformy Azure Cloud Services i Virtual Machines oferuje możliwość instalacji agenta ochrony przed złośliwym kodem dla ról PaaS i maszyn wirtualnych. W oparciu o program System Center Endpoint Protection ta funkcja zapewnia sprawdzoną lokalną technologię zabezpieczeń w chmurze.

Oferujemy [także ścisłą](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) integrację ze szczegółowymi produktami trendu i [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) na platformie Azure. Głębokie zabezpieczenia to rozwiązanie antywirusowe, a SecureCloud to rozwiązanie do szyfrowania. Głębokie zabezpieczenia są wdrażane w maszynach wirtualnych za pośrednictwem modelu rozszerzeń. Za pomocą interfejsu użytkownika Azure Portal i programu PowerShell można wybrać opcję użycia głębokiego zabezpieczenia w nowych maszynach wirtualnych, które są już wdrożone, lub istniejących maszyn wirtualnych, które wcześniej są wdrażane.

Program Symantec Endpoint Protection (SEP) jest również obsługiwany na platformie Azure. Za pomocą integracji portalu można określić, że ma być używany SEP na maszynie wirtualnej. Polecenie SEP można zainstalować na nowej maszynie wirtualnej za pośrednictwem Azure Portal lub można ją zainstalować na istniejącej maszynie wirtualnej za pomocą programu PowerShell.

Więcej informacji:

* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines](antimalware.md)
* [Jak zainstalować i skonfigurować Trend Micro głębokiego zabezpieczenia jako usługi na maszynie wirtualnej z systemem Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Jak zainstalować i skonfigurować Endpoint Protection firmy Symantec na maszynie wirtualnej z systemem Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Nowe opcje chroniące przed złośliwym kodem na potrzeby ochrony Virtual Machines platformy Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication to metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji. Powoduje to dodanie krytycznej drugiej warstwy zabezpieczeń do logowania i transakcji użytkownika.

Multi-Factor Authentication pomaga chronić dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostego procesu logowania. Zapewnia silne uwierzytelnianie za pomocą zakresu opcji weryfikacji (połączenie telefoniczne, wiadomość tekstowa lub powiadomienie lub kod weryfikacyjny aplikacji mobilnej) oraz tokenów OATH innych firm.

Więcej informacji:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co to jest usługa Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Jak działa usługa Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Możesz użyć usługi Azure ExpressRoute, aby zwiększyć możliwości sieci lokalnych do Microsoft Cloud za pośrednictwem dedykowanego połączenia prywatnego, które jest obsługiwane przez dostawcę połączenia. Dzięki usłudze ExpressRoute możesz nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Azure, Microsoft 365 i CRM Online. Połączenia mogą być następujące:

* Sieć dowolna-dowolna (IP sieci VPN).
* Sieć Ethernet typu punkt-punkt.
* Wirtualne połączenie krzyżowe za pośrednictwem dostawcy łączności w ramach funkcji wspólnej lokalizacji.

Połączenia ExpressRoute nie przechodzą przez publiczny Internet. Mogą oferować większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.

Więcej informacji:

* [ExpressRoute — opis techniczny](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Bramy sieci wirtualnej

Bramy sieci VPN, nazywane również bramami sieci wirtualnej platformy Azure, służą do wysyłania ruchu sieciowego między sieciami wirtualnymi i lokalizacjami lokalnymi. Są one również używane do wysyłania ruchu między wieloma sieciami wirtualnymi na platformie Azure (sieć do sieci). Bramy sieci VPN zapewniają bezpieczną łączność między różnymi lokalizacjami platformy Azure i infrastrukturą.

Więcej informacji:

* [Informacje o bramach sieci VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Omówienie zabezpieczeń sieci platformy Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Czasami użytkownicy muszą wykonać operacje uprzywilejowane w zasobach platformy Azure lub w innych aplikacjach SaaS. Często oznacza to, że organizacje przyznają im stały dostęp uprzywilejowany w Azure Active Directory (Azure AD).

Jest to rosnące zagrożenie bezpieczeństwa dla zasobów hostowanych w chmurze, ponieważ organizacje nie mogą wystarczająco dobrze monitorować działania użytkowników z dostępem uprzywilejowanym. Ponadto, jeśli naruszone zostanie konto użytkownika z dostępem uprzywilejowanym, oznacza to, że jedno naruszenie może mieć wpływ na ogólne zabezpieczenia chmury w organizacji. Azure AD Privileged Identity Management pomaga rozwiązać ten problem, obniżając czas ekspozycji uprawnień i zwiększając wgląd w użycie.  

Privileged Identity Management wprowadza koncepcję tymczasowego administratora dla roli lub "just in Time" dostępu administratora. Ten rodzaj administratora to użytkownik, który musi wykonać proces aktywacji dla przypisanej roli. Proces aktywacji zmienia przypisanie użytkownika do roli w usłudze Azure AD z nieaktywnej na aktywny, przez określony czas.

Więcej informacji:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Wprowadzenie do aplikacji Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection zapewnia skonsolidowany widok podejrzanych działań związanych z logowaniem i potencjalnych luk w zabezpieczeniach firmy. Ochrona tożsamości wykrywa podejrzane działania dla użytkowników i tożsamości uprzywilejowanych (administratorów) na podstawie sygnałów takich jak:

* Ataki w postaci wymuszenia.
* Przecieki poświadczeń.
* Logowania z nieznanych lokalizacji i zainfekowanych urządzeń.

Dostarczając powiadomienia i zalecaną korektę, Ochrona tożsamości pomaga w ograniczeniu ryzyka w czasie rzeczywistym. Oblicza ważność ryzyka użytkownika. Zasady oparte na podwyższonym ryzyku można skonfigurować do automatycznego zabezpieczania dostępu aplikacji przed przyszłymi zagrożeniami.

Więcej informacji:

* [Ochrona tożsamości w usłudze Azure Active Directory](/azure/active-directory/active-directory-identityprotection)
* [Channel 9: usługa Azure AD i tożsamość show: wersja zapoznawcza usługi Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Usługa Azure Security Center pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Security Center zapewnia większą widoczność i kontrolę nad bezpieczeństwem zasobów platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które w przeciwnym razie mogą być niezauważalne i działać z szerokim ekosystemem rozwiązań zabezpieczających.

Security Center pomaga zoptymalizować i monitorować zabezpieczenia zasobów platformy Azure, wykonując następujące działania:

* Umożliwienie definiowania zasad dla zasobów subskrypcji platformy Azure zgodnie z:
  * Wymagania dotyczące zabezpieczeń firmy.
  * Typ aplikacji lub czułość danych w każdej subskrypcji.
* Monitorowanie stanu maszyn wirtualnych platformy Azure, sieci i aplikacji.
* Udostępnienie listy alertów zabezpieczeń, w tym alertów z zintegrowanych rozwiązań partnerskich. Zawiera również informacje, które należy wykonać, aby szybko zbadać ataki i zalecenia dotyczące sposobu jego rozwiązania.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../../security-center/security-center-intro.md)
* [Popraw swój Bezpieczny wynik w Azure Security Center](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph zapewnia ochronę przed zagrożeniami w czasie rzeczywistym w produktach i usługach firmy Microsoft. Korzysta ona z zaawansowanej analizy, która łączy ogromną ilość danych dotyczących analizy zagrożeń i zabezpieczeń, aby zapewnić wgląd, który może wzmocnić zabezpieczenia organizacji. Firma Microsoft używa zaawansowanej analizy — przetwarzanie ponad 450 000 000 000 uwierzytelnień miesięcznie, Skanowanie wiadomości e-mail 400 000 000 000 w poszukiwaniu złośliwego oprogramowania i wyłudzania informacji oraz aktualizowanie urządzeń 1 000 000 000 — w celu zapewnienia bardziej szczegółowych informacji. Te szczegółowe informacje mogą pomóc w organizacji wykrywać ataki i reagować na nie.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [udostępnionym modelem odpowiedzialności](shared-responsibility.md) , które zadania zabezpieczeń są obsługiwane przez firmę Microsoft i które zadania są obsługiwane przez użytkownika.

Aby uzyskać więcej informacji na temat zarządzania zabezpieczeniami, zobacz [Zarządzanie zabezpieczeniami na platformie Azure](management.md).

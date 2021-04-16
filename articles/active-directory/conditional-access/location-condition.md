---
title: Warunek lokalizacji w Azure Active Directory warunkowego
description: Dowiedz się, jak za pomocą warunku lokalizacji kontrolować dostęp do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 8b46ca16fc32a7b96c071a745f49bf5d5557f34b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530232"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Używanie warunku lokalizacji w zasadach dostępu warunkowego 

Jak wyjaśniono w [artykule](overview.md) z omówieniem, zasady dostępu warunkowego są najbardziej podstawowymi instrukcje łączenia sygnałów if-then w celu podejmowania decyzji i wymuszania zasad organizacji. Jednym z tych sygnałów, które można włączyć do procesu podejmowania decyzji, jest lokalizacja sieciowa.

![Koncepcyjny sygnał warunkowy oraz decyzja o wymuszenia](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Organizacje mogą używać tej lokalizacji sieciowej do wykonywania typowych zadań, takich jak: 

- Wymaganie uwierzytelniania wieloskładnikowego dla użytkowników, którzy mają dostęp do usługi poza siecią firmową.
- Blokowanie dostępu użytkowników, którzy mają dostęp do usługi z określonych krajów lub regionów.

Lokalizacja sieciowa jest określana przez publiczny adres IP, który klient udostępnia Azure Active Directory. Zasady dostępu warunkowego domyślnie mają zastosowanie do wszystkich adresów IPv4 i IPv6. 

## <a name="named-locations"></a>Nazwane lokalizacje

Lokalizacje są wyznaczane w obszarze Azure Portal w obszarze **Azure Active Directory**  >  **Security Conditional**  >  **Access** Named locations ( Lokalizacje z  >  **dostępem warunkowym zabezpieczeń).** Te nazwane lokalizacje sieciowe mogą obejmować lokalizacje, takie jak zakresy sieci siedzib organizacji, zakresy sieci VPN lub zakresy, które chcesz zablokować. Nazwane lokalizacje mogą być definiowane przez zakresy adresów IPv4/IPv6 lub według krajów/regionów. 

![Nazwane lokalizacje w Azure Portal](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>Zakresy adresów IP

Aby zdefiniować nazwaną lokalizację według zakresów adresów IPv4/IPv6, należy podać nazwę **i** zakres adresów IP. 

Nazwane lokalizacje zdefiniowane przez zakresy adresów IPv4/IPv6 podlegają następującym ograniczeniom: 
- Konfigurowanie do 195 nazwanych lokalizacji
- Skonfiguruj maksymalnie 2000 zakresów adresów IP na nazwaną lokalizację
- Obsługiwane są zarówno zakresy IPv4, jak i IPv6
- Prywatne zakresy adresów IP nie są konfigurowane
- Liczba adresów IP zawartych w zakresie jest ograniczona. Podczas definiowania zakresu adresów IP dozwolone są tylko maski CIDR większe niż /8. 

### <a name="trusted-locations"></a>Zaufane lokalizacje

Administratorzy mogą wyznaczyć nazwane lokalizacje zdefiniowane przez zakresy adresów IP na zaufane nazwane lokalizacje. 

![Zaufane lokalizacje w Azure Portal](./media/location-condition/new-trusted-location.png)

Logowania z zaufanych nazwanych lokalizacji poprawiają dokładność obliczeń ryzyka Azure AD Identity Protection, obniżając ryzyko logowania użytkowników podczas uwierzytelniania z lokalizacji oznaczonej jako zaufana. Ponadto zaufane nazwane lokalizacje mogą być celem zasad dostępu warunkowego. Na przykład można wymagać ograniczenia rejestracji uwierzytelniania wieloskładnikowego tylko do zaufanych nazwanych lokalizacji. 

### <a name="countries-and-regions"></a>Kraje i regiony

Niektóre organizacje mogą zdecydować się na ograniczenie dostępu do niektórych krajów lub regionów przy użyciu dostępu warunkowego. Oprócz definiowania nazwanych lokalizacji według zakresów adresów IP administratorzy mogą definiować nazwane lokalizacje według kraju lub regionu. Gdy użytkownik się pojawia, usługa Azure AD rozpozna adres IPv4 użytkownika jako kraj lub region, a mapowanie jest okresowo aktualizowane. Organizacje mogą używać nazwanych lokalizacji zdefiniowanych przez kraje, aby blokować ruch z krajów, w których nie są one prowadzenia działalności, takich jak Korea Północna. 

> [!NOTE]
> Logowania z adresów IPv6 nie mogą być mapowane na kraje lub regiony i są uznawane za nieznane obszary. Tylko adresy IPv4 mogą być mapowane na kraje lub regiony.

![Utwórz nową lokalizację opartą na kraju lub regionie w Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Uwzględnianie nieznanych obszarów

Niektóre adresy IP nie są mapowane na określony kraj lub region, w tym wszystkie adresy IPv6. Aby przechwycić te lokalizacje IP, zaznacz pole wyboru **Uwzględnij nieznane obszary** podczas definiowania lokalizacji. Ta opcja umożliwia wybranie, czy te adresy IP powinny być uwzględnione w nazwanej lokalizacji. Użyj tego ustawienia, jeśli zasady używające nazwanej lokalizacji powinny być stosowane do nieznanych lokalizacji.

### <a name="configure-mfa-trusted-ips"></a>Konfigurowanie zaufanych ip usługi MFA

Zakresy adresów IP reprezentujące lokalny intranet organizacji można również skonfigurować w ustawieniach usługi [uwierzytelniania wieloskładnikowego.](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx) Ta funkcja umożliwia skonfigurowanie maksymalnie 50 zakresów adresów IP. Zakresy adresów IP są w formacie CIDR. Aby uzyskać więcej informacji, zobacz [Zaufane ip .](../authentication/howto-mfa-mfasettings.md#trusted-ips)  

Jeśli masz skonfigurowane zaufane adresy IP, są one wyświetlane jako zaufane adresy **IP usługi MFA** na liście lokalizacji dla warunku lokalizacji.

### <a name="skipping-multi-factor-authentication"></a>Pomijanie uwierzytelniania wieloskładnikowego

Na stronie ustawień usługi Multi-Factor Authentication można zidentyfikować użytkowników firmowego intranetu, wybierając pozycję Pomiń uwierzytelnianie wieloskładnikowe dla żądań od użytkowników federowanych w **moim intranecie.** To ustawienie wskazuje, że oświadczenie wewnątrz sieci firmowej, które jest wystawiane przez usługę AD FS, powinno być zaufane i używane do identyfikowania użytkownika jako użytkownika w sieci firmowej. Aby uzyskać więcej informacji, zobacz [Włączanie funkcji zaufanego dostępu ip przy użyciu dostępu warunkowego.](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)

Po wybraniu tej opcji, łącznie z nazwaną lokalizacją **ZAUFANY ADRES IP USŁUGI MFA** będą stosowane do wszystkich zasad z wybraną tą opcją.

W przypadku aplikacji mobilnych i klasycznych, które mają długie okresy istnienia sesji, okresowo jest ponownie oceniany dostęp warunkowy. Wartość domyślna to raz na godzinę. Gdy oświadczenie wewnętrznej sieci firmowej jest wystawiane tylko w czasie początkowego uwierzytelniania, usługa Azure AD może nie mieć listy zaufanych zakresów adresów IP. W takim przypadku trudniej jest określić, czy użytkownik nadal znajduje się w sieci firmowej:

1. Sprawdź, czy adres IP użytkownika należy do jednego z zaufanych zakresów adresów IP.
1. Sprawdź, czy pierwsze trzy oktety adresu IP użytkownika pasują do pierwszych trzech oktetów adresu IP początkowego uwierzytelniania. Adres IP jest porównywany z początkowym uwierzytelnianiem, gdy pierwotnie wystawiono oświadczenie wewnętrznej sieci firmowej, a lokalizacja użytkownika została zweryfikowana.

Jeśli oba kroki nie powiodą się, użytkownik nie będzie już mieć zaufanego adresu IP.

## <a name="location-condition-in-policy"></a>Warunek lokalizacji w zasadach

Podczas konfigurowania warunku lokalizacji można rozróżnić:

- Dowolnej aplikacji.
- Wszystkie zaufane lokalizacje
- Wybrane lokalizacje

### <a name="any-location"></a>Dowolnej aplikacji.

Domyślnie wybranie pozycji **Dowolna lokalizacja** powoduje zastosowanie zasad do wszystkich adresów IP, co oznacza dowolny adres w Internecie. To ustawienie nie jest ograniczone do adresów IP skonfigurowanych jako nazwana lokalizacja. Po wybraniu pozycji **Dowolna lokalizacja** nadal można wykluczyć określone lokalizacje z zasad. Można na przykład zastosować zasady do wszystkich lokalizacji z wyjątkiem zaufanych lokalizacji, aby ustawić zakres na wszystkie lokalizacje, z wyjątkiem sieci firmowej.

### <a name="all-trusted-locations"></a>Wszystkie zaufane lokalizacje

Ta opcja ma zastosowanie do:

- Wszystkie lokalizacje, które zostały oznaczone jako zaufane lokalizacje
- **Zaufane adresy IP usługi MFA** (jeśli zostały skonfigurowane)

### <a name="selected-locations"></a>Wybrane lokalizacje

Za pomocą tej opcji można wybrać co najmniej jedną nazwaną lokalizację. Aby zastosować zasady z tym ustawieniem, użytkownik musi nawiązać połączenie z dowolnej z wybranych lokalizacji. Po kliknięciu **przycisku Wybierz** nazwaną kontrolkę wyboru sieci, która wyświetla listę nazwanych sieci zostanie otwarta. Lista zawiera również informacje o tym, czy lokalizacja sieciowa została oznaczona jako zaufana. Nazwana lokalizacja o nazwie **Zaufane adresy IP usługi MFA** służy do dołączania ustawień adresu IP, które można skonfigurować na stronie ustawień usługi uwierzytelniania wieloskładnikowego.

## <a name="ipv6-traffic"></a>Ruch IPv6

Domyślnie zasady dostępu warunkowego będą stosowane do całego ruchu IPv6. Określone zakresy adresów IPv6 można wykluczyć z zasad dostępu warunkowego, jeśli nie chcesz wymuszać zasad dla określonych zakresów adresów IPv6. Jeśli na przykład nie chcesz wymuszać zasad dla zastosowań w sieci firmowej, a sieć firmowa jest hostowana w zakresach publicznych adresów IPv6.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Kiedy moja dzierżawa będzie miała ruch IPv6?

Azure Active Directory (Azure AD) nie obsługuje obecnie bezpośrednich połączeń sieciowych, które korzystają z protokołu IPv6. Istnieją jednak pewne przypadki, w których ruch uwierzytelniania jest proxied przez inną usługę. W takich przypadkach adres IPv6 będzie używany podczas oceny zasad.

Większość ruchu IPv6, który jest proxied do usługi Azure AD, pochodzi z usługi Microsoft Exchange Online. Jeśli jest dostępna, program Exchange będzie preferować połączenia IPv6. **Dlatego jeśli masz jakiekolwiek zasady dostępu warunkowego dla programu Exchange, które zostały skonfigurowane dla określonych zakresów adresów IPv4, upewnij się, że dodano również zakresy adresów IPv6 organizacji.** Nieujęcie zakresów adresów IPv6 spowoduje nieoczekiwane zachowanie w następujących dwóch przypadkach:

- Jeśli klient poczty jest używany do łączenia się z usługą Exchange Online przy użyciu starszego uwierzytelniania, usługa Azure AD może otrzymać adres IPv6. Początkowe żądanie uwierzytelniania przechodzi do programu Exchange, a następnie jest proxied do usługi Azure AD.
- Gdy program Outlook Dostęp w sieci Web (OWA) jest używany w przeglądarce, okresowo sprawdza, czy wszystkie zasady dostępu warunkowego są nadal spełnione. Ten test służy do wychwytania przypadków, w których użytkownik mógł przenieść się z dozwolonego adresu IP do nowej lokalizacji, takiej jak kawiarni przy ulicy. W takim przypadku, jeśli jest używany adres IPv6, a adres IPv6 nie znajduje się w skonfigurowanym zakresie, sesja użytkownika może zostać przerwana i skierowany z powrotem do usługi Azure AD w celu ponownego uwierzytelnienia. 

Są to najbardziej typowe przyczyny, dla których może być konieczne skonfigurowanie zakresów adresów IPv6 w nazwanych lokalizacjach. Ponadto jeśli używasz sieci wirtualnych platformy Azure, ruch będzie pochodzić z adresu IPv6. Jeśli ruch sieci wirtualnej jest blokowany przez zasady dostępu warunkowego, sprawdź dziennik logowania usługi Azure AD. Po zidentyfikowaniu ruchu możesz uzyskać używany adres IPv6 i wykluczyć go z zasad. 

> [!NOTE]
> Jeśli chcesz określić zakres ADRESÓW IP CIDR dla pojedynczego adresu, zastosuj maskę /128-bitową. Jeśli powiesz adres IPv6 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a i chcesz wykluczyć ten pojedynczy adres jako zakres, użyj wartości 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Identyfikowanie ruchu IPv6 w raportach aktywności logowania usługi Azure AD

Ruch IPv6 można wykryć w dzierżawie, przechodząc do raportów aktywności [logowania usługi Azure AD.](../reports-monitoring/concept-sign-ins.md) Po otwarciu raportu aktywności dodaj kolumnę "Adres IP". Ta kolumna umożliwia zidentyfikowanie ruchu IPv6.

Adres IP klienta można również znaleźć, klikając wiersz w raporcie, a następnie przechodząc do karty "Lokalizacja" w szczegółach działania logowania. 

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="when-is-a-location-evaluated"></a>Kiedy jest oceniana lokalizacja?

Zasady dostępu warunkowego są oceniane w przypadku:

- Użytkownik początkowo korzysta z aplikacji internetowej, aplikacji mobilnej lub klasycznej.
- Aplikacja mobilna lub desktopowa, która korzysta z nowoczesnego uwierzytelniania, używa tokenu odświeżania w celu uzyskania nowego tokenu dostępu. Domyślnie to sprawdzanie jest co godzinę.

Oznacza to, że w przypadku aplikacji mobilnych i klasycznych korzystających z nowoczesnego uwierzytelniania zmiana lokalizacji zostanie wykryta w ciągu godziny od zmiany lokalizacji sieciowej. W przypadku aplikacji mobilnych i klasycznych, które nie korzystają z nowoczesnego uwierzytelniania, zasady są stosowane do każdego żądania tokenu. Częstotliwość żądania może się różnić w zależności od aplikacji. Podobnie w przypadku aplikacji internetowych zasady są stosowane podczas początkowego logowania i są dobre w okresie istnienia sesji w aplikacji internetowej. Ze względu na różnice w okresie istnienia sesji w aplikacjach czas między oceną zasad również będzie się różnić. Za każdym razem, gdy aplikacja żąda nowego tokenu logowania, zasady są stosowane.

Domyślnie usługa Azure AD wydaje token co godzinę. Po przeniesieniu z sieci firmowej w ciągu godziny zasady są wymuszane dla aplikacji korzystających z nowoczesnego uwierzytelniania.

### <a name="user-ip-address"></a>Adres IP użytkownika

Adres IP używany w ocenie zasad to publiczny adres IP użytkownika. W przypadku urządzeń w sieci prywatnej ten adres IP nie jest adresem IP klienta urządzenia użytkownika w sieci intranet, ale adresem używanym przez sieć do łączenia się z publicznym Internetem.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Zbiorcze przekazywanie i pobieranie nazwanych lokalizacji

Podczas tworzenia lub aktualizowania nazwanych lokalizacji w przypadku aktualizacji zbiorczych można przekazać lub pobrać plik CSV z zakresami adresów IP. Przekazywanie zastępuje zakresy adresów IP na liście zakresami z pliku. Każdy wiersz pliku zawiera jeden zakres adresów IP w formacie CIDR.

### <a name="cloud-proxies-and-vpns"></a>Serwery proxy w chmurze i sieci VPN

W przypadku korzystania z serwera proxy hostowanej w chmurze lub rozwiązania sieci VPN adres IP używany przez usługę Azure AD podczas oceniania zasad jest adresem IP serwera proxy. Nagłówek X-Forwarded-For (XFF), który zawiera publiczny adres IP użytkownika, nie jest używany, ponieważ nie ma weryfikacji, czy pochodzi on z zaufanego źródła, więc przedstawi metodę fałszowania adresu IP.

Gdy serwer proxy w chmurze jest już używany, można użyć zasad, które wymagają urządzenia przyłączone hybrydową do usługi Azure AD, lub oświadczenia wewnętrznego sieci firmowej z AD FS.

### <a name="api-support-and-powershell"></a>Obsługa interfejsu API i programu PowerShell

Dostępna jest wersja zapoznawcza interfejs Graph API dla nazwanych lokalizacji. Aby uzyskać więcej informacji, zobacz [namedLocation API](/graph/api/resources/namedlocation).

> [!NOTE]
> Nazwane lokalizacje, które tworzysz za pomocą programu PowerShell, są wyświetlane tylko w nazwanych lokalizacjach (wersja zapoznawcza). Nazwane lokalizacje nie są widać w starym widoku.  

## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz artykuł [Tworzenie zasad dostępu warunkowego.](concept-conditional-access-policies.md)
- Szukasz przykładowych zasad korzystających z warunku lokalizacji? Zobacz artykuł Dostęp [warunkowy: Blokowanie dostępu według lokalizacji](howto-conditional-access-policy-location.md)

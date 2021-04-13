---
title: Warunek lokalizacji w Azure Active Directory dostęp warunkowy
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
ms.openlocfilehash: 07af586bac71ee9b33ef314756454cb3c52ec912
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305926"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Używanie warunku lokalizacji w zasadach dostępu warunkowego 

Zgodnie z opisem w [artykule Omówienie](overview.md) zasady dostępu warunkowego znajdują się najwyżej w podstawowej instrukcji if-then łączącej sygnały, podejmowania decyzji i wymuszania zasad organizacji. Jednym z tych sygnałów, które mogą być włączone do procesu podejmowania decyzji, jest lokalizacja sieciowa.

![Koncepcyjny sygnał warunkowy i decyzja o konieczności uzyskania wymuszania](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Organizacje mogą używać tej lokalizacji sieciowej do wykonywania typowych zadań, takich jak: 

- Wymaganie uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do usługi w przypadku wyłączenia sieci firmowej.
- Blokowanie dostępu dla użytkowników uzyskujących dostęp do usługi z określonych krajów lub regionów.

Lokalizacja sieciowa jest określana przez publiczny adres IP, który klient zapewnia Azure Active Directory. Zasady dostępu warunkowego są domyślnie stosowane do wszystkich adresów IPv4 i IPv6. 

## <a name="named-locations"></a>Nazwane lokalizacje

Lokalizacje są wyznaczane w Azure Portal w obszarze **Azure Active Directory**  >  **zabezpieczenia**  >  **dostępu warunkowego**  >  **o nazwie lokalizacje**. Te nazwane lokalizacje sieciowe mogą obejmować lokalizacje, takie jak zakresy sieci w organizacji, zakresy sieci VPN lub zakresy, które mają zostać zablokowane. Nazwane lokalizacje mogą być definiowane przez zakresy adresów IPv4/IPv6 lub kraje/regiony. 

![Nazwane lokalizacje w Azure Portal](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>Zakresy adresów IP

Aby zdefiniować nazwaną lokalizację według zakresów adresów IPv4/IPv6, należy podać **nazwę** i zakres adresów IP. 

Nazwane lokalizacje zdefiniowane przez zakresy adresów IPv4/IPv6 podlegają następującym ograniczeniom: 
- Skonfiguruj do 195 nazwanych lokalizacji
- Skonfiguruj maksymalnie 2000 zakresów adresów IP na nazwę lokalizacji
- Obsługiwane są zarówno zakresy adresów IPv4, jak i IPv6
- Connot zakresy prywatnych adresów IP
- Liczba adresów IP zawartych w zakresie jest ograniczona. Podczas definiowania zakresu adresów IP można używać tylko masek CIDR o wartości większej niż/8. 

### <a name="trusted-locations"></a>Zaufane lokalizacje

Administratorzy mogą wyznaczyć nazwane lokalizacje zdefiniowane przez zakresy adresów IP, które mają być zaufanymi nazwanymi lokalizacjami. 

![Zaufane lokalizacje w Azure Portal](./media/location-condition/new-trusted-location.png)

Logowania z zaufanych nazwanych lokalizacji poprawiają dokładność obliczania ryzyka Azure AD Identity Protection, co zmniejsza ryzyko związane z logowaniem użytkowników podczas uwierzytelniania z lokalizacji oznaczonej jako zaufane. Ponadto zaufane nazwane lokalizacje mogą być stosowane w zasadach dostępu warunkowego. Można na przykład wymagać ograniczenia rejestracji uwierzytelniania wieloskładnikowego tylko do zaufanych lokalizacji nazwanych. 

### <a name="countries-and-regions"></a>Kraje i regiony

Niektóre organizacje mogą ograniczyć dostęp do niektórych krajów lub regionów przy użyciu dostępu warunkowego. Oprócz definiowania nazwanych lokalizacji według zakresów adresów IP Administratorzy mogą definiować nazwane lokalizacje według kraju lub regionu. Gdy użytkownik loguje się, usługa Azure AD rozpoznaje adres IPv4 użytkownika w kraju lub regionie, a mapowanie jest okresowo aktualizowane. Organizacje mogą używać nazwanych lokalizacji zdefiniowanych przez kraje do blokowania ruchu z krajów, w których nie działają, na przykład w Korei Północnej. 

> [!NOTE]
> Logowania z adresów IPv6 nie mogą być mapowane na kraje ani regiony i są uznawane za nieznane obszary. Tylko adresy IPv4 można zamapować na kraje lub regiony.

![Tworzenie nowego kraju lub lokalizacji na podstawie regionu w Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Uwzględnij nieznane obszary

Niektóre adresy IP nie są zamapowane na określony kraj lub region, w tym wszystkie adresy IPv6. Aby przechwytywać te lokalizacje IP, zaznacz pole wyboru **Uwzględnij nieznane obszary** podczas definiowania lokalizacji. Ta opcja umożliwia wybranie, czy te adresy IP mają być uwzględnione w nazwanej lokalizacji. Użyj tego ustawienia, jeśli zasady używające nazwanej lokalizacji mają być stosowane do nieznanych lokalizacji.

### <a name="configure-mfa-trusted-ips"></a>Konfigurowanie zaufanych adresów IP usługi MFA

Możesz również skonfigurować zakresy adresów IP reprezentujące Lokalny intranet organizacji w [ustawieniach usługi uwierzytelniania wieloskładnikowego](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Ta funkcja umożliwia skonfigurowanie maksymalnie 50 zakresów adresów IP. Zakresy adresów IP są w formacie CIDR. Aby uzyskać więcej informacji, zobacz [Zaufane adresy IP](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Jeśli skonfigurowano Zaufane adresy IP, są one wyświetlane jako **Zaufane adresy IP usługi MFA** na liście lokalizacji warunku lokalizacji.

### <a name="skipping-multi-factor-authentication"></a>Pomijanie uwierzytelniania wieloskładnikowego

Na stronie Ustawienia usługi uwierzytelniania wieloskładnikowego można zidentyfikować użytkowników intranetu firmowego, wybierając pozycję  **Pomiń uwierzytelnianie wieloskładnikowe w przypadku żądań od użytkowników federacyjnych w moim intranecie**. To ustawienie wskazuje, że wewnątrz roszczeń sieci firmowej, które jest wydawane przez AD FS, powinno być zaufane i używane do identyfikowania użytkownika jako sieci firmowej. Aby uzyskać więcej informacji, zobacz [Włączanie funkcji Zaufane adresy IP przy użyciu dostępu warunkowego](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po zaznaczeniu tej opcji, w tym nazwanej **Zaufane adresy IP usługi MFA** , będą stosowane do wszystkich zasad z wybraną opcją.

W przypadku aplikacji mobilnych i klasycznych, które mają długotrwałe okresy istnienia sesji, dostęp warunkowy jest okresowo obliczany. Wartość domyślna to raz na godzinę. Gdy wystąpiło tylko w trakcie uwierzytelniania wewnętrznego, usługa Azure AD może nie mieć listy zaufanych zakresów adresów IP. W takim przypadku trudniejsze jest określenie, czy użytkownik nadal znajduje się w sieci firmowej:

1. Sprawdź, czy adres IP użytkownika należy do jednego z zaufanych zakresów adresów IP.
1. Sprawdź, czy pierwsze trzy oktety adresu IP użytkownika pasują do pierwszych trzech oktetów adresu IP początkowego uwierzytelniania. Adres IP jest porównywany z początkowym uwierzytelnianiem, gdy pierwotne twierdzenie sieci firmowej zostało wystawione i zweryfikowano lokalizację użytkownika.

Jeśli oba kroki zakończą się niepowodzeniem, użytkownik jest uznawany za niebędący już w zaufanym adresie IP.

## <a name="location-condition-in-policy"></a>Warunek lokalizacji w zasadach

Podczas konfigurowania warunku lokalizacji można rozróżnić następujące opcje:

- Dowolnej aplikacji.
- Wszystkie Zaufane lokalizacje
- Wybrane lokalizacje

### <a name="any-location"></a>Dowolnej aplikacji.

Domyślnie wybranie **dowolnej lokalizacji** powoduje, że zasady mają być stosowane do wszystkich adresów IP, co oznacza dowolny adres w Internecie. To ustawienie nie jest ograniczone do adresów IP skonfigurowanych jako nazwana lokalizacja. W przypadku wybrania **dowolnej lokalizacji** można nadal wykluczać określone lokalizacje z zasad. Na przykład można zastosować zasady do wszystkich lokalizacji, z wyjątkiem zaufanych lokalizacji, aby ustawić zakres dla wszystkich lokalizacji, z wyjątkiem sieci firmowej.

### <a name="all-trusted-locations"></a>Wszystkie Zaufane lokalizacje

Ta opcja ma zastosowanie do:

- Wszystkie lokalizacje oznaczone jako zaufane
- **Zaufane adresy IP usługi MFA** (jeśli zostały skonfigurowane)

### <a name="selected-locations"></a>Wybrane lokalizacje

Za pomocą tej opcji można wybrać jedną lub więcej nazwanych lokalizacji. Aby można było zastosować zasady z tym ustawieniem, użytkownik musi nawiązać połączenie z dowolnych z wybranych lokalizacji. Po kliknięciu przycisku **Wybierz wybraną** kontrolkę Wybieranie sieci, która wyświetla listę nazwanych sieci otwiera się. Lista pokazuje również, czy lokalizacja sieciowa została oznaczona jako zaufana. Nazwana lokalizacja o nazwie **Zaufane adresy IP usługi MFA** służy do uwzględnienia ustawień protokołu IP, które można skonfigurować na stronie Ustawienia usługi uwierzytelniania wieloskładnikowego.

## <a name="ipv6-traffic"></a>Ruch IPv6

Domyślnie zasady dostępu warunkowego będą stosowane do całego ruchu IPv6. Można wykluczyć określone zakresy adresów IPv6 z zasad dostępu warunkowego, jeśli nie chcesz, aby zasady były wymuszane dla określonych zakresów adresów IPv6. Na przykład jeśli chcesz, aby nie wymuszać zasad używanych w sieci firmowej, a sieć firmowa jest hostowana w publicznych zakresach adresów IPv6.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Kiedy moja dzierżawa będzie mieć ruch IPv6?

Azure Active Directory (Azure AD) nie obsługuje obecnie bezpośrednich połączeń sieciowych korzystających z protokołu IPv6. Istnieją jednak sytuacje, w których ruch związany z uwierzytelnianiem jest przychodzący przez inną usługę. W takich przypadkach adres IPv6 zostanie użyty podczas obliczania zasad.

Większość ruchu IPv6, który jest serwerem proxy w usłudze Azure AD pochodzi z usługi Microsoft Exchange Online. Jeśli jest dostępny, program Exchange będzie preferować połączenia IPv6. **Dlatego jeśli masz jakieś zasady dostępu warunkowego dla programu Exchange, które zostały skonfigurowane dla określonych zakresów adresów IPv4, upewnij się, że dodano również zakresy adresów IPv6 organizacji.** Nieuwzględnienie zakresów IPv6 spowoduje nieoczekiwane zachowanie dla następujących dwóch przypadków:

- Gdy klient poczty jest używany do nawiązywania połączenia z usługą Exchange Online przy użyciu starszego uwierzytelniania, usługa Azure AD może odbierać adres IPv6. Początkowe żądanie uwierzytelniania przechodzi do programu Exchange, a następnie jest przekazywane do usługi Azure AD.
- Gdy w przeglądarce jest używany program Outlook Dostęp w sieci Web (OWA), okresowo sprawdza wszystkie zasady dostępu warunkowego, które nadal będą spełnione. Ta kontrola służy do wychwycenia przypadków, w których użytkownik mógł przenieść się z dozwolonego adresu IP do nowej lokalizacji, na przykład w przypadku warsztatu w dół. W takim przypadku, jeśli używany jest adres IPv6 i jeśli adres IPv6 nie znajduje się w skonfigurowanym zakresie, może to spowodować przerwanie sesji i nastąpi przekierowanie do usługi Azure AD w celu ponownego uwierzytelnienia. 

Oto najczęstsze przyczyny, w których może być konieczne skonfigurowanie zakresów adresów IPv6 w nazwanych lokalizacjach. Ponadto, jeśli używasz usługi Azure sieci wirtualnych, będziesz mieć ruch pochodzący z adresu IPv6. Jeśli ruch sieci wirtualnej jest blokowany przez zasady dostępu warunkowego, sprawdź dziennik logowania usługi Azure AD. Po zidentyfikowaniu ruchu można uzyskać używany adres IPv6 i wykluczyć go z zasad. 

> [!NOTE]
> Aby określić zakres adresów IP CIDR dla pojedynczego adresu, należy zastosować maskę bitową/128. Jeśli adres IPv6 2607: fb90: b27a: 6f69: f8d5: dea0: fb39:74A i chciałeś wykluczyć ten pojedynczy adres jako zakres, należy użyć 2607: fb90: b27a: 6f69: f8d5: dea0: fb39:74A/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Identyfikowanie ruchu IPv6 w raportach działań związanych z logowaniem w usłudze Azure AD

Ruch IPv6 w dzierżawie można odnajdywać, przechodząc do [raportów dotyczących działań związanych z logowaniem w usłudze Azure AD](../reports-monitoring/concept-sign-ins.md). Po otwarciu raportu aktywności Dodaj kolumnę "adres IP". Ta kolumna umożliwia zidentyfikowanie ruchu IPv6.

Możesz również znaleźć adres IP klienta, klikając wiersz w raporcie, a następnie przechodząc do karty "lokalizacja" w obszarze szczegóły działania logowania. 

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="when-is-a-location-evaluated"></a>Kiedy jest szacowana lokalizacja?

Zasady dostępu warunkowego są oceniane, gdy:

- Użytkownik najpierw loguje się do aplikacji sieci Web, mobilnej lub klasycznej.
- Aplikacja mobilna lub klasyczna korzystająca z nowoczesnego uwierzytelniania korzysta z tokenu odświeżania w celu uzyskania nowego tokenu dostępu. Domyślnie to sprawdzenie jest za godzinę.

To sprawdzenie oznacza, że aplikacje mobilne i klasyczne używają nowoczesnego uwierzytelniania, zmiana lokalizacji zostanie wykryta w ciągu godziny zmiany lokalizacji sieciowej. W przypadku aplikacji mobilnych i klasycznych, które nie korzystają z nowoczesnego uwierzytelniania, zasady są stosowane do każdego żądania tokenu. Częstotliwość żądania może się różnić w zależności od aplikacji. Podobnie w przypadku aplikacji sieci Web zasady są stosowane podczas początkowego logowania i są dobre dla okresu istnienia sesji w aplikacji sieci Web. Ze względu na różnice w okresach istnienia sesji między aplikacjami, czas między oceną zasad będzie również różny. Za każdym razem, gdy aplikacja żąda nowego tokenu logowania, zasady są stosowane.

Domyślnie usługa Azure AD wystawia token co godzinę. Po przejściu z sieci firmowej w ciągu godziny zasady są wymuszane dla aplikacji korzystających z nowoczesnego uwierzytelniania.

### <a name="user-ip-address"></a>Adres IP użytkownika

Adres IP, który jest używany w ocenie zasad, to publiczny adres IP użytkownika. W przypadku urządzeń w sieci prywatnej ten adres IP nie jest adresem IP klienta urządzenia użytkownika w intranecie. jest to adres używany przez sieć do łączenia się z publicznym Internetem.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Zbiorcze przekazywanie i pobieranie nazwanych lokalizacji

Podczas tworzenia lub aktualizowania nazwanych lokalizacji w przypadku aktualizacji zbiorczych można przekazać lub pobrać plik CSV z zakresami adresów IP. Przekazywanie zastępuje zakresy adresów IP na liście tymi zakresami z pliku. Każdy wiersz pliku zawiera jeden zakres adresów IP w formacie CIDR.

### <a name="cloud-proxies-and-vpns"></a>Serwery proxy w chmurze i sieci VPN

W przypadku korzystania z serwera proxy hostowanego w chmurze lub rozwiązania sieci VPN adres IP używany przez usługę Azure AD podczas oceniania zasad jest adresem IP serwera proxy. Nagłówek X-Forwarded-For (XFF), który zawiera publiczny adres IP użytkownika, nie jest używany, ponieważ nie ma weryfikacji, że pochodzi on z zaufanego źródła, dlatego należy przedstawić metodę faking adresu IP.

Gdy serwer proxy w chmurze jest używany, można użyć zasad, które są wymagane do zażądania hybrydowego urządzenia dołączonego do usługi Azure AD lub z poziomu AD FS.

### <a name="api-support-and-powershell"></a>Obsługa interfejsu API i programu PowerShell

Dostępna jest wersja zapoznawcza interfejs API programu Graph dla nazwanych lokalizacji. Aby uzyskać więcej informacji, zobacz [interfejs API namedLocation](/graph/api/resources/namedlocation?view=graph-rest-beta).

> [!NOTE]
> Nazwane lokalizacje tworzone przy użyciu programu PowerShell są wyświetlane tylko w nazwanych lokalizacjach (wersja zapoznawcza). Nazwanych lokalizacji nie można zobaczyć w starym widoku.  

## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz artykuł [Tworzenie zasad dostępu warunkowego](concept-conditional-access-policies.md).
- Szukasz przykładowych zasad przy użyciu warunku lokalizacji? Zapoznaj się z artykułem [dostęp warunkowy: Blokuj dostęp według lokalizacji](howto-conditional-access-policy-location.md)

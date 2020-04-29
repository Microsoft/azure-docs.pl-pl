---
title: Warunek lokalizacji w Azure Active Directory dostęp warunkowy
description: Dowiedz się, jak za pomocą warunku lokalizacji kontrolować dostęp do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79263233"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Jaki jest warunek lokalizacji w Azure Active Directory dostęp warunkowy? 

Za pomocą [dostępu warunkowego Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)można kontrolować sposób, w jaki autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze. Warunek lokalizacji zasad dostępu warunkowego umożliwia powiązanie ustawień kontroli dostępu z lokalizacjami sieciowymi użytkowników.

Ten artykuł zawiera informacje potrzebne do skonfigurowania warunku lokalizacji.

## <a name="locations"></a>Lokalizacje

Usługa Azure AD umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca w publicznym Internecie. Warunek lokalizacji pozwala kontrolować dostęp do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika. Typowe przypadki użycia dla warunku lokalizacji są następujące:

- Wymaganie uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do usługi w przypadku wyłączenia sieci firmowej.
- Blokowanie dostępu dla użytkowników uzyskujących dostęp do usługi z określonych krajów lub regionów.

Lokalizacja to etykieta lokalizacji sieciowej, która reprezentuje nazwaną lokalizację lub Zaufane adresy IP usługi uwierzytelniania wieloskładnikowego.

## <a name="named-locations"></a>Nazwane lokalizacje

Przy użyciu nazwanych lokalizacji można tworzyć logiczne grupowania zakresów adresów IP lub krajów i regionów.

Dostęp do nazwanych lokalizacji można uzyskać w sekcji **Zarządzanie** na stronie dostęp warunkowy.

![Nazwane lokalizacje w dostępie warunkowym](./media/location-condition/02.png)

Nazwana Lokalizacja ma następujące składniki:

![Utwórz nową nazwę lokalizacji](./media/location-condition/42.png)

- **Nazwa** — wyświetlana nazwa lokalizacji.
- **Zakresy IP** — co najmniej jeden zakres adresów IPv4 w formacie CIDR. Określanie zakresu adresów IPv6 nie jest obsługiwane.

   > [!NOTE]
   > W nazwanej lokalizacji nie można obecnie uwzględnić zakresów adresów IPv6. Oznacza to, że zakresy adresów IPv6 nie mogą być wykluczone z zasad dostępu warunkowego.

- **Oznacz jako zaufaną lokalizację** — flagę, którą można ustawić dla nazwanej lokalizacji, aby wskazać zaufaną lokalizację. Zazwyczaj Zaufane lokalizacje to obszary sieci, które są kontrolowane przez dział IT. Oprócz dostępu warunkowego zaufane nazwane lokalizacje są również używane przez usługę Azure Identity Protection i raporty zabezpieczeń usługi Azure AD w celu zmniejszenia liczby [fałszywych dodatnich](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Kraje/regiony** — ta opcja umożliwia wybranie co najmniej jednego kraju lub regionu w celu zdefiniowania nazwanej lokalizacji.
- **Uwzględnij nieznane obszary** — niektóre adresy IP nie są zamapowane do określonego kraju lub regionu. Ta opcja umożliwia wybranie, czy te adresy IP mają być uwzględnione w nazwanej lokalizacji. Użyj tego ustawienia, jeśli zasady używające nazwanej lokalizacji mają być stosowane do nieznanych lokalizacji.

Liczba nazwanych lokalizacji, które można skonfigurować, jest ograniczona przez rozmiar powiązanego obiektu w usłudze Azure AD. Lokalizacje można skonfigurować w oparciu o następujące ograniczenia:

- Jedna nazwana lokalizacja z maksymalnie 1200 zakresami adresów IP.
- Maksymalnie 90 nazwanych lokalizacji z jednym zakresem adresów IP przypisanym do każdego z nich.

Zasady dostępu warunkowego dotyczą ruchu IPv4 i IPv6. Obecnie nazwane lokalizacje nie umożliwiają konfigurowania zakresów adresów IPv6. To ograniczenie powoduje następujące sytuacje:

- Zasady dostępu warunkowego nie mogą być wskazywane dla określonych zakresów adresów IPv6
- Zasady dostępu warunkowego nie mogą wykluczać określonych zakresów adresów IPV6

Jeśli zasady są skonfigurowane do zastosowania do "dowolnej lokalizacji", będą stosowane do ruchu IPv4 i IPv6. Nazwane lokalizacje skonfigurowane dla określonych krajów i regionów obsługują tylko adresy IPv4. Ruch IPv6 jest uwzględniany tylko wtedy, gdy wybrano opcję "Uwzględnij nieznane obszary".

## <a name="trusted-ips"></a>Zaufane adresy IP

Możesz również skonfigurować zakresy adresów IP reprezentujące Lokalny intranet organizacji w [ustawieniach usługi uwierzytelniania wieloskładnikowego](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Ta funkcja umożliwia skonfigurowanie maksymalnie 50 zakresów adresów IP. Zakresy adresów IP są w formacie CIDR. Aby uzyskać więcej informacji, zobacz [Zaufane adresy IP](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Jeśli skonfigurowano Zaufane adresy IP, są one wyświetlane jako **Zaufane adresy IP usługi MFA** na liście lokalizacji warunku lokalizacji.

### <a name="skipping-multi-factor-authentication"></a>Pomijanie uwierzytelniania wieloskładnikowego

Na stronie Ustawienia usługi uwierzytelniania wieloskładnikowego można zidentyfikować użytkowników intranetu firmowego, wybierając pozycję **Pomiń uwierzytelnianie wieloskładnikowe w przypadku żądań od użytkowników federacyjnych w moim intranecie**. To ustawienie wskazuje, że wewnątrz roszczeń sieci firmowej, które jest wydawane przez AD FS, powinno być zaufane i używane do identyfikowania użytkownika jako sieci firmowej. Aby uzyskać więcej informacji, zobacz [Włączanie funkcji Zaufane adresy IP przy użyciu dostępu warunkowego](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po zaznaczeniu tej opcji, w tym nazwanej **Zaufane adresy IP usługi MFA** , będą stosowane do wszystkich zasad z wybraną opcją.

W przypadku aplikacji mobilnych i klasycznych, które mają długotrwałe okresy istnienia sesji, dostęp warunkowy jest okresowo obliczany. Wartość domyślna to raz na godzinę. Gdy wystąpiło tylko w trakcie uwierzytelniania wewnętrznego, usługa Azure AD może nie mieć listy zaufanych zakresów adresów IP. W takim przypadku trudniejsze jest określenie, czy użytkownik nadal znajduje się w sieci firmowej:

1. Sprawdź, czy adres IP użytkownika należy do jednego z zaufanych zakresów adresów IP.
2. Sprawdź, czy pierwsze trzy oktety adresu IP użytkownika pasują do pierwszych trzech oktetów adresu IP początkowego uwierzytelniania. Adres IP jest porównywany z początkowym uwierzytelnianiem, gdy pierwotne twierdzenie sieci firmowej zostało wystawione i zweryfikowano lokalizację użytkownika.

Jeśli oba kroki zakończą się niepowodzeniem, użytkownik jest uznawany za niebędący już w zaufanym adresie IP.

## <a name="location-condition-configuration"></a>Konfiguracja warunku lokalizacji

Podczas konfigurowania warunku lokalizacji można rozróżnić następujące opcje:

- Dowolnej aplikacji.
- Wszystkie Zaufane lokalizacje
- Wybrane lokalizacje

![Konfiguracja warunku lokalizacji](./media/location-condition/01.png)

### <a name="any-location"></a>Dowolnej aplikacji.

Domyślnie wybranie **dowolnej lokalizacji** powoduje, że zasady mają być stosowane do wszystkich adresów IP, co oznacza dowolny adres w Internecie. To ustawienie nie jest ograniczone do adresów IP skonfigurowanych jako nazwana lokalizacja. W przypadku wybrania **dowolnej lokalizacji**można nadal wykluczać określone lokalizacje z zasad. Na przykład można zastosować zasady do wszystkich lokalizacji, z wyjątkiem zaufanych lokalizacji, aby ustawić zakres dla wszystkich lokalizacji, z wyjątkiem sieci firmowej.

### <a name="all-trusted-locations"></a>Wszystkie Zaufane lokalizacje

Ta opcja ma zastosowanie do:

- Wszystkie lokalizacje oznaczone jako zaufane
- **Zaufane adresy IP usługi MFA** (jeśli zostały skonfigurowane)

### <a name="selected-locations"></a>Wybrane lokalizacje

Za pomocą tej opcji można wybrać jedną lub więcej nazwanych lokalizacji. Aby można było zastosować zasady z tym ustawieniem, użytkownik musi nawiązać połączenie z dowolnych z wybranych lokalizacji. Po kliknięciu przycisku **Wybierz wybraną** kontrolkę Wybieranie sieci, która wyświetla listę nazwanych sieci otwiera się. Lista pokazuje również, czy lokalizacja sieciowa została oznaczona jako zaufana. Nazwana lokalizacja o nazwie **Zaufane adresy IP usługi MFA** służy do uwzględnienia ustawień protokołu IP, które można skonfigurować na stronie Ustawienia usługi uwierzytelniania wieloskładnikowego.

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="when-is-a-location-evaluated"></a>Kiedy jest szacowana lokalizacja?

Zasady dostępu warunkowego są oceniane, gdy:

- Użytkownik najpierw loguje się do aplikacji sieci Web, mobilnej lub klasycznej.
- Aplikacja mobilna lub klasyczna korzystająca z nowoczesnego uwierzytelniania korzysta z tokenu odświeżania w celu uzyskania nowego tokenu dostępu. Domyślnie to sprawdzenie jest za godzinę.

To sprawdzenie oznacza, że aplikacje mobilne i klasyczne używają nowoczesnego uwierzytelniania, zmiana lokalizacji zostanie wykryta w ciągu godziny zmiany lokalizacji sieciowej. W przypadku aplikacji mobilnych i klasycznych, które nie korzystają z nowoczesnego uwierzytelniania, zasady są stosowane do każdego żądania tokenu. Częstotliwość żądania może się różnić w zależności od aplikacji. Podobnie w przypadku aplikacji sieci Web zasady są stosowane podczas początkowego logowania i są dobre dla okresu istnienia sesji w aplikacji sieci Web. Ze względu na różnice w okresach istnienia sesji między aplikacjami, czas między oceną zasad będzie również różny. Za każdym razem, gdy aplikacja żąda nowego tokenu logowania, zasady są stosowane.

Domyślnie usługa Azure AD wystawia token co godzinę. Po przejściu z sieci firmowej w ciągu godziny zasady są wymuszane dla aplikacji korzystających z nowoczesnego uwierzytelniania.

### <a name="user-ip-address"></a>Adres IP użytkownika

Adres IP, który jest używany w ocenie zasad, to publiczny adres IP użytkownika. W przypadku urządzeń w sieci prywatnej ten adres IP nie jest adresem IP klienta urządzenia użytkownika w intranecie. jest to adres używany przez sieć do łączenia się z publicznym Internetem.

> [!WARNING]
> Jeśli urządzenie ma tylko adres IPv6, skonfigurowanie warunku lokalizacji nie jest obsługiwane.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Zbiorcze przekazywanie i pobieranie nazwanych lokalizacji

Podczas tworzenia lub aktualizowania nazwanych lokalizacji w przypadku aktualizacji zbiorczych można przekazać lub pobrać plik CSV z zakresami adresów IP. Przekazywanie zastępuje zakresy adresów IP na liście tymi z pliku. Każdy wiersz pliku zawiera jeden zakres adresów IP w formacie CIDR.

### <a name="cloud-proxies-and-vpns"></a>Serwery proxy w chmurze i sieci VPN

W przypadku korzystania z serwera proxy hostowanego w chmurze lub rozwiązania sieci VPN adres IP używany przez usługę Azure AD podczas oceniania zasad jest adresem IP serwera proxy. Nagłówek X-Forwarded-For (XFF), który zawiera publiczny adres IP użytkownika, nie jest używany, ponieważ nie ma weryfikacji, że pochodzi on z zaufanego źródła, dlatego należy przedstawić metodę faking adresu IP.

Gdy serwer proxy w chmurze jest na miejscu, można użyć zasad, które są używane do wymagania urządzenia przyłączonego do domeny, lub wewnątrz roszczeń sieci firmowej z AD FS.

### <a name="api-support-and-powershell"></a>Obsługa interfejsu API i programu PowerShell

Interfejsy API i PowerShell nie są jeszcze obsługiwane dla nazwanych lokalizacji ani dla zasad dostępu warunkowego.

## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji, Azure Active Directory dostępu warunkowego](app-based-mfa.md).
- Aby skonfigurować zasady dostępu warunkowego dla środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](best-practices.md).

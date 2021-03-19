---
title: Funkcje usługi Azure firewall Premium w wersji zapoznawczej
description: Azure firewall Premium to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby Virtual Network platformy Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 4a8efff7ef53753e15a47e87a2bb82d0124ae997
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590453"
---
# <a name="azure-firewall-premium-preview-features"></a>Funkcje usługi Azure firewall Premium w wersji zapoznawczej

Logo certyfikatu :::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA" border="false":::logo:::image type="content" source="media/premium-features/pci-logo.png" alt-text="certyfikatu PCI" border="false":::


> [!IMPORTANT]
> Usługa Azure firewall Premium jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Zapora Azure Premium w wersji zapoznawczej to Zapora nowej generacji z możliwościami, które są wymagane w środowiskach wysoce wrażliwych i regulowanych.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Diagram omówienia usługi Azure firewall Premium":::

Zapora Azure Premium w wersji zapoznawczej korzysta z zasad zapory, zasobu globalnego, którego można użyć do centralnego zarządzania zaporami przy użyciu Menedżera zapory platformy Azure. Począwszy od tej wersji, wszystkie nowe funkcje można konfigurować tylko za pomocą zasad zapory. Reguły zapory (klasyczne) są nadal obsługiwane i mogą być używane do konfigurowania istniejących funkcji zapory standardowej.  Zasady zapory mogą być zarządzane niezależnie lub za pomocą Menedżera zapory platformy Azure. Zasady zapory skojarzone z pojedynczą zaporą nie są naliczane.

> [!IMPORTANT]
> Obecnie jednostka SKU warstwy Premium zapory nie jest obsługiwana w przypadku wdrożeń przy użyciu protokołów Secure Hub i wymuszonych konfiguracji tunelu. 

Usługa Azure firewall Premium w wersji zapoznawczej obejmuje następujące funkcje:

- **Inspekcja TLS** — odszyfrowuje ruch wychodzący, przetwarza dane, a następnie szyfruje dane i wysyła je do miejsca docelowego.
- **Dostawców tożsamości** — system wykrywania i zapobiegania włamaniom w sieci (dostawców tożsamości) pozwala monitorować działania sieciowe w poszukiwaniu złośliwego działania, rejestrować informacje o tym działaniu, zgłaszać je i opcjonalnie próbować go zablokować.
- **Filtrowanie adresów URL** — rozszerza możliwości filtrowania nazw FQDN zapory platformy Azure w celu uwzględnienia całego adresu URL. Na przykład `www.contoso.com/a/c` zamiast `www.contoso.com` .
- **Kategorie sieci Web** — Administratorzy mogą zezwalać użytkownikom na dostęp do takich kategorii witryn sieci Web, jak witryny hazard, witryny sieci Web mediów społecznościowych i inne osoby, jak na nie.


## <a name="tls-inspection"></a>Inspekcja protokołu TLS

Zapora platformy Azure w warstwie Premium kończy połączenia z protokołem TLS i wyjściem z zachodniego Wschodu. Inspekcja ruchu przychodzącego TLS jest obsługiwana przez [platformę Azure Application Gateway](../web-application-firewall/ag/ag-overview.md) , która umożliwia kompleksowe szyfrowanie. Zapora platformy Azure wykonuje wymagane funkcje zabezpieczeń i ponownie szyfruje ruch wysyłany do oryginalnego miejsca docelowego.

> [!TIP]
> Protokoły TLS 1,0 i 1,1 są przestarzałe i nie będą obsługiwane. Okazało się, że protokół TLS 1,0 i 1,1 wersje protokołu TLS/Secure Sockets Layer (SSL) są zagrożone, a mimo to nadal działają tak, aby umożliwić zgodność z poprzednimi wersjami, nie są one zalecane. Migruj do protokołu TLS 1,2 najszybciej, jak to możliwe.

Aby dowiedzieć się więcej o wymaganiach dotyczących certyfikatu pośredniego urzędu certyfikacji usługi Azure firewall Premium, zobacz [Azure firewall Premium Preview Certificates](premium-certificates.md).

## <a name="idps"></a>DOSTAWCÓW tożsamości

System wykrywania i zapobiegania włamaniom w sieci (dostawców tożsamości) umożliwia monitorowanie sieci pod kątem złośliwego działania, rejestrowanie informacji o tym działaniu, zgłaszanie go i opcjonalnie podejmowanie próby jego zablokowania. 

Usługa Azure firewall Premium w wersji zapoznawczej zapewnia dostawców tożsamości oparte na sygnaturach, dzięki czemu można szybko wykrywać ataki, szukając określonych wzorców, takich jak sekwencje bajtów w ruchu sieciowym lub znane sekwencje złośliwych instrukcji używane przez złośliwe oprogramowanie. Podpisy dostawców tożsamości są w pełni zarządzane i stale aktualizowane.

Sygnatury/zestawu reguł zapory platformy Azure obejmują:
- Nacisk na odciski palców rzeczywiste złośliwe oprogramowanie, polecenia i kontrola, zestawy wykorzystujące luki w zabezpieczeniach, a w przypadku złośliwych, szkodliwych działań nieodebranych przez tradycyjne metody zapobiegania.
- Ponad 35 000 reguł w ponad 50 kategorii.
    - Kategorie obejmują polecenie i kontrolę nad złośliwym oprogramowaniem, ataki w systemie DoS, botnetami, zdarzenia informacyjne, luki w zabezpieczeniach, SCADAe protokoły sieciowe, działania w programie Exploit Kit i nie tylko.
- od 20 do 40 nowych reguł są wydawanych każdego dnia.
- Niska fałszywa Ocena dodatnia przy użyciu najbardziej graficznego obszaru piaskownicy złośliwego oprogramowania i pętli opinii globalnej sieci czujników.

Program dostawców tożsamości pozwala wykrywać ataki na wszystkich portach i protokołach dla nieszyfrowanego ruchu sieciowego. Jeśli jednak należy przeprowadzić inspekcję ruchu HTTPS, Zapora platformy Azure może używać funkcji inspekcji TLS do odszyfrowywania ruchu i lepszego wykrywania złośliwych działań.  

Lista obejścia dostawców tożsamości umożliwia filtrowanie ruchu do dowolnych adresów IP, zakresów i podsieci określonych na liście pomijania. 

## <a name="url-filtering"></a>Filtrowanie adresów URL

Filtrowanie adresów URL rozszerza możliwości filtrowania nazw FQDN zapory platformy Azure w celu uwzględnienia całego adresu URL. Na przykład `www.contoso.com/a/c` zamiast `www.contoso.com` .  

Filtrowanie adresów URL może być stosowane zarówno w przypadku ruchu HTTP, jak i HTTPS. Po sprawdzeniu ruchu HTTPS Zapora Azure Premium w wersji zapoznawczej może użyć funkcji inspekcji TLS w celu odszyfrowania ruchu i wyodrębnienia docelowego adresu URL w celu sprawdzenia, czy dostęp jest dozwolony. Inspekcja TLS wymaga zgody na poziomie reguły aplikacji. Po włączeniu można używać adresów URL do filtrowania przy użyciu protokołu HTTPS. 

## <a name="web-categories"></a>Kategorie sieci Web

Kategorie sieci Web umożliwiają administratorom Zezwalanie na dostęp użytkowników do kategorii witryn sieci Web, takich jak witryny typu hazard, witryny internetowe mediów społecznościowych i inne osoby. Kategorie sieci Web również zostaną uwzględnione w standardzie zapory platformy Azure, ale będzie to bardziej precyzyjnie dopasowane w wersji zapoznawczej usługi Azure firewall Premium. W przeciwieństwie do kategorii sieci Web w standardowej jednostce SKU, która jest zgodna z kategorią opartą na nazwie FQDN, jednostka SKU Premium dopasowuje kategorię zgodnie z całym adresem URL dla ruchu HTTP i HTTPS. 

Na przykład jeśli Zapora platformy Azure przechwytuje żądanie HTTPS dla `www.google.com/news` , oczekiwana jest następująca Kategoryzacja: 

- Zapora standardowa — zostanie zbadana tylko część nazwy FQDN, więc `www.google.com` zostanie ona skategoryzowana jako *aparat wyszukiwania*. 

- Zapora Premium — zostanie sprawdzony pełny adres URL, więc `www.google.com/news` zostanie on skategoryzowany jako *wiadomości*.

Kategorie są zorganizowane na podstawie ważności **odpowiedzialności**, **dużej przepustowości**, **użycia w firmie**, **utraty produktywności**, **ogólnego żeglowania** i bez **kategorii**.

### <a name="category-exceptions"></a>Wyjątki kategorii

Można utworzyć wyjątki dla reguł kategorii sieci Web. Utwórz oddzielną kolekcję reguł Zezwól lub Odmów z wyższym priorytetem w grupie kolekcji reguł. Można na przykład skonfigurować kolekcję reguł, która umożliwia korzystanie `www.linkedin.com` z priorytetu 100 z kolekcją reguł, która odmówi **sieci społecznościowych** z priorytetem 200. Spowoduje to utworzenie wyjątku dla wstępnie zdefiniowanej kategorii sieci Web **Sieć społecznościowa** .

### <a name="categorization-change"></a>Zmiana kategoryzacji

Możesz zażądać zmiany kategoryzacji, jeśli:

 - należy wziąć pod uwagę, że nazwa FQDN lub adres URL powinny znajdować się w innej kategorii 
 
lub 

- masz sugerowaną kategorię dla nazwy FQDN lub adresu URL bez kategorii

Witamy w przesłaniu żądania o [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .
 
## <a name="supported-regions"></a>Obsługiwane regiony

Usługa Azure firewall Premium w wersji zapoznawczej jest obsługiwana w następujących regionach:

- Europa Zachodnia (Public/Europa)
- Wschodnie stany USA (Public/Stany Zjednoczone)
- Australia Wschodnia (Public/Australia)
- Azja Południowo-Wschodnia (Public/Azja i Pacyfik)
- Południowe Zjednoczone Królestwo (Public/Wielka Brytania)
- Europa Północna (Public/Europa)
- Wschodnie stany USA 2 (publiczne/Stany Zjednoczone)
- Południowo-środkowe stany USA (publiczne/Stany Zjednoczone)
- Zachodnie stany USA 2 (publiczne/Stany Zjednoczone)
- Zachodnie stany USA (publiczne/Stany Zjednoczone)
- Środkowe stany USA (publiczne/Stany Zjednoczone)
- Północno-środkowe stany USA (publiczne/Stany Zjednoczone)
- Japonia Wschodnia (Public/Japonia)
- Azja Wschodnia (Public/Azja i Pacyfik)
- Kanada Środkowa (Public/Kanada)
- Francja Środkowa (Public/Francja)
- Północna Republika Południowej Afryki (Public/Republika Południowej Afryki)
- Północne Zjednoczone Emiraty Arabskie (Public/Zjednoczone Emiraty Arabskie)
- Szwajcaria Północna (Public/Szwajcaria)
- Brazylia Południowa (Public/Brazylia)
- Norwegia Wschodnia (Public/Norwegia)
- Australia Środkowa (Public/Australia)
- Australia Środkowa 2 (Public/Australia)
- Australia Południowo-Wschodnia (Public/Australia)
- Kanada Wschodnia (Public/Kanada)
- Środkowe stany USA — EUAP (Public/Kanaryjskie (USA))
- Francja Południowa (Public/Francja)
- Japonia Zachodnia (Public/Japonia)
- Korea Południowa (Public/Korea)
- Europa Środkowa (Public/Zjednoczone Emiraty Arabskie)
- Zachodnie Zjednoczone Królestwo (Public/Wielka Brytania)
- Zachodnio-środkowe stany USA (Public/Stany Zjednoczone)
- Indie Zachodnie (Public/Indie)


## <a name="known-issues"></a>Znane problemy

Zapora Azure w wersji Premium Preview zawiera następujące znane problemy:

|Problem  |Description  |Ograniczanie ryzyka  |
|---------|---------|---------|
|Inspekcja TLS jest obsługiwana tylko w standardowym porcie HTTPS|Inspekcja TLS obsługuje tylko protokół HTTPS/443. |Brak. Inne porty będą obsługiwane w wersji ogólnie dostępnej.|
|ESNI obsługa rozpoznawania nazw FQDN w protokole HTTPS|Szyfrowanie SNI nie jest obsługiwane w uzgadnianiu HTTPS.|Obecnie tylko Firefox obsługuje ESNI za poorednictwem konfiguracji niestandardowej. Zalecane obejście to wyłączenie tej funkcji.|
|Certyfikaty klienta (TLS)|Certyfikaty klienta są używane do tworzenia wzajemnego zaufania tożsamości między klientem a serwerem. Certyfikaty klienta są używane podczas negocjacji TLS. Zapora platformy Azure ponownie negocjuje połączenie z serwerem i nie ma dostępu do klucza prywatnego certyfikatów klienta.|Brak|
|PROTOKÓŁ QUIC/HTTP3|Protokół QUIC to nowa wersja główna protokołu HTTP. Jest to protokół oparty na protokole UDP over 80 (PLAN) i 443 (SSL). Nie jest obsługiwana Inspekcja nazw FQDN/adresów URL/TLS.|Skonfiguruj przekazywanie protokołu UDP 80/443 jako reguły sieciowe.|
|Usługa Secure Hub i Wymuszone tunelowanie nie jest obsługiwane w wersji Premium|Obecnie jednostka SKU warstwy Premium zapory nie jest obsługiwana w przypadku wdrożeń przy użyciu protokołów Secure Hub i wymuszonych konfiguracji tunelu.|Naprawa zaplanowana na GA.|
Niezaufane certyfikaty podpisane przez klienta|Certyfikaty podpisane przez klienta nie są zaufane przez zaporę po odebraniu od intranetowego serwera sieci Web.|Naprawa zaplanowana na GA.
|Nieprawidłowe źródłowe i docelowe adresy IP w alertach dla dostawców tożsamości z inspekcją TLS.|Po włączeniu inspekcji protokołu TLS i dostawców tożsamości problemy z nowym alertem wyświetlany źródłowy/docelowy adres IP jest niewłaściwy (wewnętrzny adres IP jest wyświetlany zamiast oryginalnego adresu IP).|Naprawa zaplanowana na GA.|
|Nieprawidłowy źródłowy adres IP w alertach z dostawców tożsamości dla protokołu HTTP (bez inspekcji TLS).|Gdy jest używany ruch HTTP w postaci zwykłego tekstu, a dostawców tożsamości wystawia nowy Alert, a miejscem docelowym jest publiczny adres IP, wyświetlany źródłowy adres IP jest niewłaściwy (wewnętrzny adres IP jest wyświetlany zamiast oryginalnego adresu IP).|Naprawa zaplanowana na GA.|
|Propagacja certyfikatu|Po zastosowaniu certyfikatu urzędu certyfikacji w zaporze może upłynąć od 5-10 minut, zanim certyfikat zacznie obowiązywać.|Naprawa zaplanowana na GA.|
|Obejście dostawców tożsamości|Obejście dostawców tożsamości nie działa w przypadku ruchu przerwanego protokołu TLS, a źródłowe adresy IP i źródłowe grupy adresów IP nie są obsługiwane.|Naprawa zaplanowana na GA.|
|Obsługa protokołu TLS 1,3|Protokół TLS 1,3 jest częściowo obsługiwany. Tunel protokołu TLS od klienta do zapory jest oparty na protokole TLS 1,2, a Zapora na zewnętrzny serwer sieci Web jest oparta na protokole TLS 1,3.|Trwa badanie aktualizacji.|
|Prywatny punkt końcowy magazynu kluczy|Magazyn kluczy obsługuje dostęp do prywatnego punktu końcowego w celu ograniczenia jego ekspozycji w sieci. Zaufane usługi platformy Azure mogą ominąć to ograniczenie, jeśli został skonfigurowany wyjątek opisany w [dokumentacji magazynu](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)kluczy. Zapora platformy Azure nie jest obecnie wymieniona jako usługa zaufana i nie może uzyskać dostępu do Key Vault.|Naprawa zaplanowana na GA.|


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o certyfikatach usługi Azure firewall Premium](premium-certificates.md)
- [Wdrażanie i Konfigurowanie usługi Azure firewall Premium Preview](premium-deploy.md)
- [Migrowanie do usługi Azure firewall Premium Preview](premium-migrate.md)

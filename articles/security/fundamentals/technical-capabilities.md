---
title: Techniczne funkcje zabezpieczeń na platformie Azure — Microsoft Azure
description: Wprowadzenie do usług zabezpieczeń na platformie Azure, które ułatwiają ochronę danych, zasobów i aplikacji w chmurze.
services: security
author: terrylanfear
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 07/13/2020
ms.author: terrylan
ms.openlocfilehash: 3dd209d90947ebef7eb7b074992a55305005e7bb
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399537"
---
# <a name="azure-security-technical-capabilities"></a>Możliwości techniczne zabezpieczeń platformy Azure
Ten artykuł zawiera wprowadzenie do usług zabezpieczeń na platformie Azure, które ułatwiają ochronę danych, zasobów i aplikacji w chmurze oraz zaspokajanie potrzeb firmy.

## <a name="azure-platform"></a>Platforma Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) to platforma w chmurze obejmująca usługi infrastruktury i aplikacji oraz zintegrowane usługi danych oraz zaawansowaną analizę oraz narzędzia i usługi programistyczne hostowane w publicznych centrach danych firmy Microsoft. Klienci korzystają z platformy Azure w przypadku wielu różnych możliwości i scenariuszy, od podstawowych obliczeń, sieci i magazynu, do usług mobilnych i aplikacji sieci Web, do pełnych scenariuszy chmurowych, takich jak Internet rzeczy, i mogą być używane z technologiami typu open source i wdrażane jako chmura hybrydowa lub hostowane w centrum danych klienta. System Azure zapewnia technologię chmury jako bloki konstrukcyjne, aby pomóc firmom w oszczędnościach i szybszym wprowadzaniu innowacji oraz zarządzaniu systemami. Podczas kompilowania lub migrowania zasobów IT do dostawcy usług w chmurze polegasz na możliwościach tej organizacji w ochronie aplikacji i danych za pomocą usługi i kontroli, które zapewniają Zarządzanie zabezpieczeniami zasobów opartych na chmurze.

Microsoft Azure jest jedynym dostawcą obliczeniowym w chmurze, który oferuje bezpieczną, spójną platformę aplikacji i infrastrukturę jako usługę dla zespołów, które pracują w ramach różnych umiejętności i poziomów złożoności projektu, dzięki zintegrowanym usługom danych i analizom, które odkrywają dane z danych, wszędzie tam, gdzie się znajdują, zarówno na platformach firmy Microsoft, jak i innych firm, można otwierać platformy i narzędzia, zapewniając możliwość integracji chmury z lokalnymi usługami w chmurze platformy Azure w lokalnych centrach danych. W ramach zaufanej chmury firmy Microsoft klienci korzystają z platformy Azure w celu uzyskania wiodących w branży zabezpieczeń, niezawodności, zgodności, prywatności i szerokiej sieci osób, partnerów i procesów do obsługi organizacji w chmurze.

Za pomocą Microsoft Azure można:

- Przyspiesz innowacje z chmurą.

- Podejmuj decyzje biznesowe & aplikacji z wglądem w szczegółowe dane.

- Twórz swobodnie i wdrażaj w dowolnym miejscu.

- Chroń swoją firmę.

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Techniczne funkcje zabezpieczeń do realizacji Twojej odpowiedzialności

Microsoft Azure udostępnia usługi, które pomagają sprostać wymaganiom związanym z bezpieczeństwem, ochroną prywatności i zgodnością. Na poniższej ilustracji przedstawiono różne usługi platformy Azure, które umożliwiają utworzenie bezpiecznej i zgodnej infrastruktury aplikacji opartej na standardach branżowych.

![Dostępne możliwości techniczne zabezpieczeń — Big Picture](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Zarządzanie tożsamościami i dostępem użytkowników oraz ich kontrolowanie

Platforma Azure pomaga w ochronie danych firmowych i osobistych, umożliwiając Zarządzanie tożsamościami i poświadczeniami użytkowników oraz kontrolę dostępu.

### <a name="azure-active-directory"></a>Azure Active Directory

Rozwiązania do zarządzania tożsamościami i dostępem firmy Microsoft ułatwiają ochronę dostępu do aplikacji i zasobów w centrum danych firmy oraz w chmurze, co pozwala na dodatkowe poziomy weryfikacji, takie jak uwierzytelnianie wieloskładnikowe i zasady dostępu warunkowego. Monitorowanie podejrzanej aktywności przy użyciu zaawansowanych raportów zabezpieczeń, inspekcji i alertów umożliwia rozwiązywanie potencjalnych problemów z zabezpieczeniami. [Azure Active Directory — wersja Premium](../../active-directory/active-directory-whatis.md) zapewnia Logowanie jednokrotne do tysięcy aplikacji w chmurze i dostęp do aplikacji sieci Web uruchamianych lokalnie.

Zalety zabezpieczeń Azure Active Directory (Azure AD) obejmują:

- Utwórz jedną tożsamość dla każdego użytkownika w całym przedsiębiorstwie hybrydowym i zarządzaj nią, synchronizując użytkowników, grupy i urządzenia.

- Zapewniaj Logowanie jednokrotne do swoich aplikacji, w tym tysiące wstępnie zintegrowanych aplikacji SaaS.

- Zabezpieczyć dostęp do aplikacji przez wymuszenie uwierzytelniania wieloskładnikowego na podstawie zasad zarówno dla aplikacji lokalnych, jak i dla aplikacji w chmurze.

- Zapewnij bezpieczny dostęp zdalny do lokalnych aplikacji sieci Web za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD.

[Portal Azure Active Directory](https://aad.portal.azure.com/) jest dostępny w ramach Azure Portal. Z tego pulpitu nawigacyjnego można zapoznać się z omówieniem stanu organizacji oraz łatwo zarządzać dostępem do katalogu, użytkowników lub aplikacji.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Poniżej przedstawiono podstawowe możliwości usługi Azure Identity Management:

- Logowanie jednokrotne

- Uwierzytelnianie wieloskładnikowe

- Monitorowanie zabezpieczeń, alerty i raporty oparte na uczeniu maszynowym

- Zarządzanie tożsamościami i dostępem klientów

- Rejestracja urządzenia

- Privileged Identity Management

- Identity Protection

#### <a name="single-sign-on"></a>Logowanie jednokrotne

Logowanie jednokrotne [(SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) oznacza możliwość uzyskania dostępu do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności, logując się tylko raz przy użyciu jednego konta użytkownika. Po zalogowaniu możesz uzyskać dostęp do wszystkich aplikacji, których potrzebujesz, bez konieczności uwierzytelniania (na przykład wpisz hasło) drugi raz.

Wiele organizacji korzysta z aplikacji SaaS (Software as a Service), takich jak Microsoft 365, Box i Salesforce, na potrzeby produktywności użytkowników końcowych. W przeszłości pracownicy działu IT musieli indywidualnie utworzyć i zaktualizować konta użytkowników w każdej aplikacji SaaS, a użytkownicy musieli zapamiętać hasło dla każdej aplikacji SaaS.

[Usługa Azure AD rozszerza Active Directory lokalne na chmurę](../../active-directory/manage-apps/what-is-single-sign-on.md), umożliwiając użytkownikom używanie ich podstawowych kont organizacyjnych do logowania się do urządzeń przyłączonych do domeny i zasobów firmy, ale także wszystkich aplikacji sieci Web i SaaS wymaganych dla ich zadań.

Nie tylko użytkownicy nie muszą zarządzać wieloma zestawami nazw użytkowników i haseł. dostęp do aplikacji może być inicjowany automatycznie lub nieobsługiwany w oparciu o grupy organizacyjne i ich stan jako pracownika. [Usługa Azure AD wprowadza kontrolę zabezpieczeń i dostępu](../../active-directory/active-directory-enterprise-apps-manage-sso.md) , która umożliwia centralne zarządzanie dostępem użytkowników w aplikacjach SaaS.

#### <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

[Azure Multi-Factor Authentication (MFA)](../../active-directory/authentication/multi-factor-authentication.md) to metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji i dodaje krytyczną drugą warstwę zabezpieczeń do logowania i transakcji użytkownika. Usługa [MFA pomaga w zabezpieczeniu](../../active-directory/authentication/concept-mfa-howitworks.md) dostępu do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostego procesu logowania. Zapewnia silne uwierzytelnianie dzięki szerokiemu zakresowi opcji weryfikacji — połączenie telefoniczne, wiadomość SMS lub powiadomienie aplikacji mobilnej lub kod weryfikacyjny oraz tokeny OAuth innych firm.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorowanie zabezpieczeń, alerty i raporty oparte na uczeniu maszynowym

Monitorowanie zabezpieczeń i alerty oraz raporty oparte na uczeniu maszynowym, które identyfikują niespójne wzorce dostępu, mogą pomóc w ochronie Twojej firmy. Możesz użyć raportów dotyczących dostępu i użycia Azure Active Directory, aby uzyskać wgląd w integralność i bezpieczeństwo katalogu organizacji. Dzięki tym informacjom administrator katalogu może lepiej określić, gdzie mogą być dostępne zagrożenia bezpieczeństwa, tak aby mogły one być odpowiednio zaplanowane w celu ograniczenia ryzyka.

W Azure Portal lub za pomocą [portalu Azure Active Directory](https://aad.portal.azure.com/) [raporty](../../active-directory/active-directory-reporting-azure-portal.md) są podzielone na następujące sposoby:

- Raporty anomalii — zawierają zdarzenia logowania, które okazały się nietypowe. Naszym celem jest świadome tego działania i umożliwienie podjęcia decyzji o tym, czy zdarzenie jest podejrzane.

- Zintegrowane raporty aplikacji — zapewniają wgląd w sposób używania aplikacji w chmurze w organizacji. Azure Active Directory oferuje integrację z tysiącami aplikacji w chmurze.

- Raporty o błędach — wskazuje błędy, które mogą wystąpić podczas aprowizacji kont do aplikacji zewnętrznych.

- Raporty specyficzne dla użytkownika — wyświetla dane dotyczące urządzenia i logowania dla określonego użytkownika.

- Dzienniki aktywności — zawierają rekord wszystkich zdarzeń poddawanych inspekcji w ciągu ostatnich 24 godzin, ostatnich 7 dni lub ostatnich 30 dni, a także zmiany aktywności grup oraz działania resetowania i rejestracji hasła.

#### <a name="consumer-identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem klientów

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) to usługa zarządzania tożsamościami o wysokiej dostępności dla aplikacji przeznaczonych dla klientów, która skaluje się do setek milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Konsumenci mogą logować się do wszystkich aplikacji przy użyciu w pełni dostosowywanego procesu, używając istniejących kont sieci społecznościowych lub tworząc nowe poświadczenia.

W przeszłości deweloperzy aplikacji, którzy chcieli zarejestrować się [i logują](../../active-directory-b2c/overview.md) się do swoich aplikacji, chcą napisać własny kod. Musieli korzystać z lokalnych baz danych lub systemów do przechowywania nazw i haseł użytkowników. Azure Active Directory B2C oferuje organizacji lepszy sposób integrowania zarządzania tożsamościami konsumentów z aplikacjami za pomocą bezpiecznej, opartej na standardach platformy i dużego zestawu rozszerzalnych zasad.

W przypadku korzystania z Azure Active Directory B2C klienci mogą zarejestrować się w aplikacjach przy użyciu istniejących kont społecznościowych (Facebook, Google, Amazon, LinkedIn) lub tworząc nowe poświadczenia (adres e-mail i hasło albo nazwę użytkownika i hasło).

#### <a name="device-registration"></a>Rejestracja urządzenia

[Rejestracja urządzeń w usłudze Azure AD](../../active-directory/devices/overview.md) jest podstawą dla scenariuszy [dostępu warunkowego](../../active-directory/devices/overview.md) opartego na urządzeniach. Po zarejestrowaniu urządzenia usługa rejestracji urządzeń w usłudze Azure AD zapewnia urządzeniu tożsamość, która jest używana do uwierzytelniania urządzenia podczas logowania użytkownika. Uwierzytelnionego urządzenia i atrybutów urządzenia można następnie użyć do wymuszania zasad dostępu warunkowego dla aplikacji hostowanych w chmurze i lokalnie.

W połączeniu z rozwiązaniem do [zarządzania urządzeniami przenośnymi (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) , takim jak usługa Intune, atrybuty urządzenia w Azure Active Directory są aktualizowane przy użyciu dodatkowych informacji o urządzeniu. Dzięki temu można tworzyć reguły dostępu warunkowego, które wymuszają dostęp z urządzeń, aby spełniały standardy zabezpieczeń i zgodności.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

Usługa [Azure Active Directory (AD) Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) umożliwia zarządzanie i monitorowanie uprzywilejowanych tożsamości oraz dostęp do zasobów w usłudze Azure AD, a także innych usługi online takich jak Microsoft 365 lub Microsoft Intune.

Czasami użytkownicy muszą wykonywać operacje uprzywilejowane na platformie Azure lub w ramach zasobów Microsoft 365 lub innych aplikacji SaaS. Często oznacza to, że organizacje muszą udzielić im stałego dostępu uprzywilejowanego w usłudze Azure AD. Jest to rosnące zagrożenie bezpieczeństwa dla zasobów hostowanych w chmurze, ponieważ organizacje nie mogą wystarczająco dobrze monitorować działania użytkowników z uprawnieniami administratora. Ponadto w przypadku naruszenia zabezpieczeń konta użytkownika z dostępem uprzywilejowanym może to mieć wpływ na ogólne zabezpieczenia chmury. Azure AD Privileged Identity Management pomaga rozwiązać ten problem.

Azure AD Privileged Identity Management pozwala:

- Zobacz, którzy użytkownicy są administratorami usługi Azure AD

- Włącz dostęp administracyjny "just in Time" na żądanie do usług online firmy Microsoft, takich jak Microsoft 365 i Intune

- Pobierz raporty o historii dostępu administratora i zmianach w przypisaniach administratorów

- Uzyskiwanie alertów dotyczących dostępu do roli uprzywilejowanej

#### <a name="identity-protection"></a>Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) to usługa zabezpieczeń, która zapewnia skonsolidowany wgląd w wykrywanie ryzyka i potencjalne luki w zabezpieczeniach wpływających na tożsamości organizacji. Usługa Identity Protection używa istniejących funkcji wykrywania anomalii w Azure Active Directory (dostępnych za pośrednictwem raportów o nietypowych działaniach w usłudze Azure AD) i wprowadza nowe typy wykrywania ryzyka, które mogą wykrywać anomalie w czasie rzeczywistym.

## <a name="secure-resource-access"></a>Bezpieczny dostęp do zasobów

Kontrola dostępu na platformie Azure zaczyna się od perspektywy rozliczania. Właściciel konta platformy Azure, do którego uzyskuje dostęp [centrum konta platformy Azure](https://account.windowsazure.com/subscriptions), jest administratorem konta (AA). Subskrypcje są kontenerami do rozliczeń, ale również działają jako granice zabezpieczeń: Każda subskrypcja ma administratora usługi (SA), który umożliwia dodawanie, usuwanie i modyfikowanie zasobów platformy Azure w ramach tej subskrypcji przy użyciu Azure Portal. Domyślnym pakietem Software Assurance nowej subskrypcji jest AA, ale AA może zmienić skojarzenia zabezpieczeń w Centrum konta platformy Azure.

![Bezpieczny dostęp do zasobów na platformie Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Subskrypcje mają także skojarzenie z katalogiem. Katalog definiuje zbiór użytkowników. Mogą to być użytkownicy z pracy lub szkoły, która utworzyła katalog, lub mogą być użytkownikami zewnętrznymi (czyli kontami Microsoft). Subskrypcje są dostępne dla podzestawu tych użytkowników katalogów, którzy zostali przypisani jako administrator usługi (SA) lub współadministrator (CA); Jedynym wyjątkiem jest to, że w przypadku starszych powodów konta Microsoft (wcześniej identyfikator Windows Live ID) można przypisać jako SA lub CA bez obecności w katalogu.

Firmy zorientowane na zabezpieczenia powinny skupić się na umożliwieniu pracownikom dokładnych wymaganych uprawnień. Zbyt wiele uprawnień może uwidocznić konto w osobach atakujących. Zbyt mało uprawnień oznacza, że pracownicy nie mogą wydajnie pracować. [Kontrola dostępu oparta na rolach (Azure RBAC) na](../../role-based-access-control/overview.md) platformie Azure pomaga rozwiązać ten problem, oferując szczegółowe zarządzanie dostępem na platformie Azure.

![Bezpieczny dostęp do zasobów](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Przy użyciu kontroli dostępu opartej na rolach można przeprowadzić segregowanie zadań w ramach zespołu i nadać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań. Zamiast udzielać każdemu nieograniczonyemu dostępowi do subskrypcji lub zasobów platformy Azure, możesz zezwolić tylko na niektóre akcje. Na przykład użyj RBAC, aby umożliwić jednemu pracownikowi zarządzanie maszynami wirtualnymi w ramach subskrypcji, podczas gdy inna usługa może zarządzać bazami danych SQL w ramach tej samej subskrypcji.

![Bezpieczny dostęp do zasobów na platformie Azure (RBAC)](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Bezpieczeństwo i szyfrowanie danych

Jednym z kluczy ochrony danych w chmurze jest uwzględnienie możliwych stanów, w których mogą wystąpić dane, oraz tego, jakie kontrolki są dostępne dla tego stanu. W przypadku najlepszych rozwiązań dotyczących zabezpieczeń i szyfrowania danych platformy Azure zalecenia dotyczą następujących stanów danych.

- W spoczynku: obejmuje to wszystkie obiekty magazynu informacji, kontenery i typy, które znajdują się statycznie na nośniku fizycznym, jako dysk magnetyczny lub optyczny.
- W tranzycie: w przypadku przesyłania danych między składnikami, lokalizacjami lub programami, takimi jak sieć, przez magistralę usług (od lokalnego do chmury i na odwrót, łącznie z połączeniami hybrydowymi, takimi jak ExpressRoute) lub w procesie wejścia/wyjścia, uważa się, że jest on ruchem.

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Szyfrowanie w spoczynku zostało szczegółowo omówione w temacie [szyfrowanie danych na platformie Azure](encryption-atrest.md).

### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania

Ochrona danych w tranzycie powinna być istotną częścią strategii ochrony danych. Ponieważ dane są przenoszone z wielu lokalizacji, ogólne zalecenie polega na tym, że do wymiany danych między różnymi lokalizacjami są zawsze używane protokoły SSL/TLS. W niektórych sytuacjach może być konieczne odizolowanie całego kanału komunikacyjnego między infrastrukturą lokalną i chmurą przy użyciu wirtualnej sieci prywatnej (VPN).

W przypadku danych przenoszonych między infrastrukturą lokalną i platformą Azure należy rozważyć odpowiednie zabezpieczenia, takie jak HTTPS lub VPN.

W przypadku organizacji, które muszą zabezpieczyć dostęp z wielu stacji roboczych znajdujących się lokalnie na platformie Azure, użyj [sieci VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

W przypadku organizacji, które muszą zabezpieczyć dostęp z jednej stacji roboczej zlokalizowanej lokalnie na platformę Azure, użyj [sieci VPN typu punkt-lokacja](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Większe zestawy danych można przenieść za pomocą dedykowanego, szybkiego łącza sieci WAN, takiego jak [ExpressRoute](https://azure.microsoft.com/services/expressroute/). W przypadku wybrania opcji używania ExpressRoute można także szyfrować dane na poziomie aplikacji przy użyciu [protokołu SSL/TLS](https://support.microsoft.com/kb/257591) lub innych protokołów w celu zapewnienia dodatkowej ochrony.

W przypadku korzystania z usługi Azure Storage za pośrednictwem witryny Azure Portal wszystkie transakcje odbywają się za pośrednictwem protokołu HTTPS. [Interfejs API REST magazynu](https://msdn.microsoft.com/library/azure/dd179355.aspx) za pośrednictwem protokołu HTTPS może być również używany do współpracy z [usługą Azure Storage](https://azure.microsoft.com/services/storage/) i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organizacje, które nie chronią przesyłanych danych, są bardziej podatne na [ataki typu man-in-the-Middle](https://technet.microsoft.com/library/gg195821.aspx), [podsłuchiwanie](https://technet.microsoft.com/library/gg195641.aspx)i przejmowanie sesji. Takie ataki mogą być pierwszym krokiem do uzyskania dostępu do poufnych danych.

Więcej informacji na temat opcji sieci VPN platformy Azure można znaleźć w artykule [Planowanie i projektowanie VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="enforce-file-level-data-encryption"></a>Wymuś szyfrowanie danych na poziomie plików

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) używa zasad szyfrowania, tożsamości i autoryzacji, aby ułatwić Zabezpieczanie plików i wiadomości e-mail. Azure RMS działa na wielu urządzeniach — telefonach, tabletach i komputerach, chroniąc je zarówno w ramach organizacji, jak i poza nią. Jest to możliwe, ponieważ Azure RMS dodaje poziom ochrony, który pozostanie wraz z danymi nawet wtedy, gdy opuszcza granice organizacji.

Jeśli używasz Azure RMS do ochrony plików, korzystasz z kryptografii Standard Industry z pełną obsługą [standardu FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). W przypadku korzystania z Azure RMS na potrzeby ochrony danych użytkownik ma pewność, że ochrona jest pozostawać w pliku, nawet jeśli jest ona kopiowana do magazynu nieobjętego kontrolą IT, takiego jak usługa magazynu w chmurze. Taka sama występuje w przypadku plików udostępnianych za pośrednictwem poczty e-mail, a plik jest chroniony jako załącznik do wiadomości e-mail, z instrukcjami dotyczącymi sposobu otwierania chronionego załącznika.
Planując wdrożenie Azure RMS zalecamy wykonanie następujących czynności:

- Zainstaluj [aplikację RMS sharing](https://technet.microsoft.com/library/dn339006.aspx). Ta aplikacja integruje się z aplikacjami pakietu Office, instalując dodatek dla pakietu Office, dzięki czemu użytkownicy mogą łatwo chronić pliki bezpośrednio.

- Skonfiguruj aplikacje i usługi do obsługi Azure RMS

- Utwórz [Szablony niestandardowe](https://technet.microsoft.com/library/dn642472.aspx) odzwierciedlające wymagania biznesowe. Na przykład: szablon najważniejszych danych tajnych, który powinien zostać zastosowany we wszystkich najważniejszych wiadomościach e-mail związanych z kluczami tajnymi.

Organizacje, które mają słaby dostęp do [klasyfikacji danych](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) i ochrony plików, mogą być bardziej podatne na wycieki danych. Bez odpowiedniej ochrony plików organizacje nie będą w stanie uzyskać wglądu w dane biznesowe, monitorować pod kątem nadużycia i zapobiegać złośliwemu dostępowi do plików.

> [!Note]
> Więcej informacji na temat Azure RMS można uzyskać, czytając artykuł [wprowadzenie z usługą Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application"></a>Zabezpieczanie aplikacji
Mimo że platforma Azure jest odpowiedzialna za Zabezpieczanie infrastruktury i platformy, na której działa aplikacja, jest odpowiedzialna za zabezpieczenie samej aplikacji. Innymi słowy, należy w bezpieczny sposób opracowywać i wdrażać kod i zawartość aplikacji oraz zarządzać nimi. Bez tego, kod aplikacji lub zawartość nadal mogą być narażone na zagrożenia.

### <a name="web-application-firewall"></a>Zapora aplikacji internetowej
[Zapora aplikacji sieci Web (WAF)](../../application-gateway/waf-overview.md) to funkcja [Application Gateway](../../application-gateway/overview.md) , która zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach.

Zapora aplikacji internetowej zapewnia ochronę na podstawie reguł z [podstawowych zestawów reguł OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) w wersji 3.0 lub 2.2.9. Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Wśród nich często zdarzają się np. ataki polegające na iniekcji SQL i ataki z użyciem skryptów wykorzystywanych w wielu witrynach. Zapobieganie takim atakom z poziomu kodu aplikacji może być trudne. Może też wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na poziomie wielu warstw topologii aplikacji. Scentralizowana zapora aplikacji internetowej ułatwia zarządzanie zabezpieczeniami oraz zapewnia lepszą ochronę administratorów aplikacji przed zagrożeniami i intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy Application Gateway można łatwo przekonwertować na bramę Application Gateway obsługującą zaporę aplikacji internetowej.

Oto niektóre typowe luki w zabezpieczeniach w Internecie, przed którymi chroni zapora aplikacji internetowej:

- Ochrona przed atakami polegającymi na iniekcji SQL

- Ochrona przed atakami z użyciem skryptów wykorzystywanych w obrębie wielu witryn

- Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

- Ochrona przed naruszeniami protokołu HTTP

- Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

- Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

- Wykrywanie typowych niespotykanych konfiguracji aplikacji (czyli Apache, IIS itp.)

> [!Note]
> Aby zapoznać się z bardziej szczegółową listą reguł i ich ochrony, zobacz następujące [podstawowe zestawy reguł](../../application-gateway/waf-overview.md):

Platforma Azure udostępnia również kilka łatwych w użyciu funkcji, które ułatwiają Zabezpieczanie ruchu przychodzącego i wychodzącego dla aplikacji. Platforma Azure pomaga również klientom zabezpieczyć kod aplikacji, dostarczając zewnętrznie dostępne funkcje skanowania aplikacji sieci Web pod kątem luk w zabezpieczeniach.

- [Konfigurowanie uwierzytelniania Azure Active Directory aplikacji](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Zabezpieczanie ruchu do aplikacji przez włączenie Transport Layer Security (TLS/SSL) — HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Wymuś cały ruch przychodzący za pośrednictwem połączenia HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Włącz ścisłe zabezpieczenia transportu (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Ograniczanie dostępu do aplikacji przez adres IP klienta](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Ograniczanie dostępu do aplikacji przez zachowanie klienta — częstotliwość żądań i współbieżność](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Skanuj kod aplikacji sieci Web pod kątem luk w zabezpieczeniach przy użyciu skanowania zabezpieczeń usługa TINFOIL](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurowanie wzajemnego uwierzytelniania TLS, aby wymagać certyfikatów klienta do łączenia się z aplikacją internetową](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Skonfiguruj certyfikat klienta do użycia w aplikacji w celu bezpiecznego łączenia się z zasobami zewnętrznymi](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Usuń standardowe nagłówki serwera, aby uniknąć używania odcisków palca aplikacji przez narzędzia](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Bezpieczne łączenie aplikacji z zasobami w sieci prywatnej przy użyciu sieci VPN typu punkt-lokacja](../../app-service/web-sites-integrate-with-vnet.md)

- [Bezpiecznie łącz swoją aplikację z zasobami w sieci prywatnej przy użyciu Połączenia hybrydowe](../../app-service/app-service-hybrid-connections.md)

Azure App Service korzysta z tego samego rozwiązania chroniącego przed złośliwym kodem, które jest używane przez usługę Azure Cloud Services i Virtual Machines. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [dokumentacją ochrony przed złośliwym oprogramowaniem](antimalware.md).

## <a name="secure-your-network"></a>Zabezpieczanie sieci
Microsoft Azure obejmuje niezawodną infrastrukturę sieciową do obsługi wymagań dotyczących łączności aplikacji i usług. Możliwe jest połączenie sieciowe między zasobami znajdującymi się na platformie Azure, między zasobami lokalnymi i hostowanymi przez platformę Azure oraz z Internetu i platformy Azure.

[Infrastruktura sieci platformy Azure](../../virtual-machines/windows/infrastructure-example.md) umożliwia bezpieczne łączenie zasobów platformy Azure ze sobą przy użyciu [sieci wirtualnych (sieci wirtualnych)](../../virtual-network/virtual-networks-overview.md). Sieć wirtualna jest reprezentacją własnej sieci w chmurze. Sieć wirtualna jest logiczną izolacją sieci w chmurze platformy Azure dedykowaną dla Twojej subskrypcji. Możesz połączyć sieci wirtualnych z sieciami lokalnymi.

![Zabezpieczanie sieci (ochrona)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Jeśli potrzebujesz podstawowej kontroli dostępu na poziomie sieci (na podstawie adresu IP i protokołów TCP lub UDP), możesz użyć [grup zabezpieczeń sieci](../../virtual-network/virtual-network-vnet-plan-design-arm.md). Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń) to podstawowa Zapora filtrowania pakietów stanowych, która umożliwia kontrolowanie dostępu na podstawie [5-spójnej kolekcji](https://www.techopedia.com/definition/28190/5-tuple).

Usługa Azure Networks obsługuje możliwość dostosowywania zachowania routingu dla ruchu sieciowego w sieciach wirtualnych platformy Azure. W tym celu można skonfigurować [trasy zdefiniowane przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md) na platformie Azure.

[Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) jest mechanizmem, którego można użyć, aby upewnić się, że usługi nie mogą inicjować połączenia z urządzeniami w Internecie.

Platforma Azure obsługuje dedykowane połączenia WAN z siecią lokalną i Virtual Network platformy Azure z usługą [ExpressRoute](../../expressroute/expressroute-introduction.md). Link między platformą Azure a lokacją korzysta z dedykowanego połączenia, które nie przechodzi przez publiczny Internet. Jeśli aplikacja platformy Azure działa w wielu centrach danych, możesz użyć [usługi azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) , aby skierować żądania od użytkowników w sposób inteligentny między wystąpieniami aplikacji. Możesz również kierować ruch do usług nieuruchomionych na platformie Azure, jeśli są one dostępne z Internetu.

## <a name="virtual-machine-security"></a>Zabezpieczenia maszyn wirtualnych

[Usługa Azure Virtual Machines](../../virtual-machines/index.yml) umożliwia wdrażanie szerokiego zakresu rozwiązań obliczeniowych w sposób Agile. Dzięki obsłudze systemów Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP i Azure BizTalk Services można wdrożyć dowolne obciążenie i dowolny język w niemal dowolnym systemie operacyjnym.

Na platformie Azure możesz używać [oprogramowania chroniącego przed złośliwym kodem](antimalware.md) od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro i Kaspersky pomogą, aby chronić maszyny wirtualne przed złośliwymi plikami, programami wywiadowczymi i innymi zagrożeniami.

Firma Microsoft chroniąca przed złośliwym kodem Cloud Services i Virtual Machines to funkcja ochrony w czasie rzeczywistym, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących i innego złośliwego oprogramowania. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft zapewnia konfigurowalne alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje się zainstalować lub uruchomić w swoich systemach platformy Azure.

[Azure Backup](../../backup/backup-overview.md) to skalowalne rozwiązanie chroniące dane aplikacji bez inwestycji kapitałowych i minimalnych kosztów operacyjnych. Błędy aplikacji mogą uszkodzić dane, a błędy ludzkie mogą wprowadzać usterki do aplikacji. W przypadku Azure Backup maszyny wirtualne z systemem Windows i Linux są chronione.

[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) pomaga organizować replikację, pracę w trybie failover oraz odzyskiwanie obciążeń i aplikacji, dzięki czemu są one dostępne z lokalizacji dodatkowej, jeśli lokalizacja główna ulegnie awarii.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Zapewnianie zgodności: Lista kontrolna usługi Cloud Services z przyczyną

Firma Microsoft opracowała [listę kontrolną Cloud Services z należytym](https://aka.ms/cloudchecklist.download) nadzorem, aby ułatwić organizacjom podejmowanie pomyślnych problemów, ponieważ rozważają one przechodzenie do chmury. Zapewnia ona strukturę organizacji dowolnego rozmiaru i typu — prywatne firmy i organizacje sektora publicznego, w tym rządy na wszystkich poziomach i w niezarobkach — w celu zidentyfikowania własnej wydajności, usług, zarządzania danymi oraz celów i wymogów związanych z zarządzaniem. Dzięki temu można porównać oferty różnych dostawców usług w chmurze, co stanowi podstawę do zawarcia umowy dotyczącej usługi w chmurze.

Lista kontrolna zawiera strukturę, która dopasowuje klauzulę klauzula by do nowej międzynarodowej normy dla umów usług w chmurze, ISO/IEC 19086. Ten standard oferuje ujednolicony zestaw zagadnień dla organizacji, które ułatwiają podejmowanie decyzji dotyczących wdrażania w chmurze, a ponadto tworzy wspólną podstawę do porównywania ofert usług w chmurze.

Lista kontrolna promuje dokładnie zbadane w chmurze, zapewniając strukturalne wskazówki i spójne, powtarzalne podejście do wybierania dostawcy usług w chmurze.

Wdrażanie w chmurze nie jest już po prostu rozwiązaniem. Ze względu na to, że wymagania dotyczące listy kontrolnej są zgodne z każdym aspektem organizacji, służą one do zwołania wszystkich najważniejszych wewnętrznych decyzji — CIO i CISO, a także dla specjalistów ds. zarządzania ryzykiem, zaopatrzenia i zgodności. Zwiększa to wydajność procesu podejmowania decyzji i podejmuje decyzje dotyczące uziemienia, co zmniejsza prawdopodobieństwo nieprzewidzianego przeszkody.

Ponadto lista kontrolna:

- Uwidacznia najważniejsze tematy dotyczące podejmowania decyzji na początku procesu wdrażania chmury.

- Obsługuje dokładne dyskusje biznesowe dotyczące rozporządzeń i własnych celów organizacji dotyczących prywatności, danych osobowych i bezpieczeństwa danych.

- Pomaga organizacjom identyfikować potencjalne problemy, które mogą mieć wpływ na projekt w chmurze.

- Zapewnia spójny zestaw pytań z tymi samymi postanowieniami, definicjami, metrykami i elementami dostarczanymi dla każdego dostawcy, aby uprościć proces porównywania ofert od różnych dostawców usług w chmurze.

## <a name="azure-infrastructure-and-application-security-validation"></a>Sprawdzanie poprawności infrastruktury i zabezpieczeń aplikacji platformy Azure

[Zabezpieczenia operacyjne platformy Azure](operational-security.md) odnoszą się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony danych, aplikacji i innych zasobów w Microsoft Azure.

![Weryfikacja zabezpieczeń (wykrywanie)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Usługa Azure Operational Security jest oparta na strukturze, która obejmuje wiedzę uzyskaną za pomocą różnych funkcji, które są unikatowe dla firmy Microsoft, w tym Microsoft Security Development Lifecycle (SDL), programu Microsoft Security Response Centre i dogłębnej świadomości dotyczącej zagrożeń cyberbezpieczeństwa.

### <a name="microsoft-azure-monitor"></a>Monitor Microsoft Azure

[Azure monitor](../../azure-monitor/index.yml) to rozwiązanie do zarządzania IT w chmurze hybrydowej. Dzienniki Azure Monitor używane samodzielnie lub w celu rozbudowania istniejącego wdrożenia programu System Center oferują maksymalną elastyczność i kontrolę w zakresie zarządzania infrastrukturą opartą na chmurze.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Za pomocą Azure Monitor możesz zarządzać dowolnym wystąpieniem w dowolnej chmurze, w tym lokalnym, platformą Azure, AWS, Windows Server, Linux, VMware i OpenStack, z niższym kosztem niż w przypadku rozwiązań konkurencyjnych. Zaprojektowana na świecie w chmurze, Azure Monitor oferuje nowe podejście do zarządzania przedsiębiorstwem, które jest najszybszym i ekonomicznym sposobem sprostania nowym wyzwaniom biznesowym i uwzględnieniu nowych obciążeń, aplikacji i środowisk chmury.

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki Azure monitor](https://azure.microsoft.com/documentation/services/log-analytics) udostępniają usługi monitorowania przez zbieranie danych z zarządzanych zasobów do centralnego repozytorium. Te dane mogą obejmować zdarzenia, dane wydajności i niestandardowe dane dostarczane przez interfejs API. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu.

![Dzienniki usługi Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Ta metoda umożliwia konsolidowanie danych z różnych źródeł, dzięki czemu można połączyć dane z usług platformy Azure z istniejącym środowiskiem lokalnym. Ponadto wprowadza wyraźny podział między zbieraniem danych a akcjami wykonanymi na tych danych, tak aby wszystkie akcje były dostępne dla wszystkich typów danych.

### <a name="azure-security-center"></a>Azure Security Center

Usługa [Azure Security Center](../../security-center/security-center-intro.md) ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Centrum zabezpieczeń analizuje stan zabezpieczeń zasobów platformy Azure w celu identyfikowania potencjalnych luk w zabezpieczeniach. Lista zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych elementów sterujących.

Przykłady:

- Inicjowanie ochrony przed złośliwym oprogramowaniem w celu identyfikacji i usuwania złośliwego oprogramowania

- Konfigurowanie grup zabezpieczeń sieci i reguł w celu kontrolowania ruchu do maszyn wirtualnych

- Inicjowanie zapór aplikacji internetowych pomagających chronić przed atakami na aplikacje internetowe użytkownika

- Wdrażanie brakujących aktualizacji systemu

- Modyfikowanie konfiguracji systemu operacyjnego, które odbiegają od zalecanych standardów

Centrum zabezpieczeń automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak programy chroniące przed złośliwym oprogramowaniem i zapory. Po wykryciu zagrożenia tworzony jest alert zabezpieczeń. Przykłady obejmują wykrywanie:

- Naruszone maszyny wirtualne komunikują się ze znanymi złośliwymi adresami IP

- Zaawansowanego złośliwego oprogramowania wykrytego za pomocą funkcji raportowania błędów systemu Windows

- Wymuszanie ataków na maszyny wirtualne

- Alerty zabezpieczeń ze zintegrowanych programów chroniących przed złośliwym oprogramowaniem i zapór

### <a name="azure-monitor"></a>Monitor platformy Azure

[Azure monitor](../../azure-monitor/overview.md) zawiera wskaźniki do informacji o określonych typach zasobów. Oferuje wizualizacje, zapytania, routing, alerty, automatyczne skalowanie i automatyzację danych zarówno z infrastruktury platformy Azure (Dziennik aktywności), jak i poszczególnych zasobów platformy Azure (dzienników diagnostycznych).

Aplikacje w chmurze są złożone z wieloma ruchomymi częściami. Monitorowanie zapewnia dane, aby upewnić się, że aplikacja będzie działać w dobrej kondycji. Pomaga również w staveniu potencjalnych problemów lub rozwiązywaniu problemów z poprzednimi.

![Azure Monitor ](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png) Ponadto możesz użyć monitorowania danych, aby uzyskać szczegółowe informacje o aplikacji. Ta wiedza może pomóc w zwiększeniu wydajności aplikacji lub utrzymaniu lub zautomatyzowaniu działań, które w przeciwnym razie wymagają ręcznej interwencji.

Inspekcja zabezpieczeń sieci jest konieczna do wykrywania luk w zabezpieczeniach sieci i zapewnienia zgodności z modelem nadzoru obowiązującym w zakresie bezpieczeństwa i zarządzania. Widok grupy zabezpieczeń umożliwia pobranie skonfigurowanych sieciowych grup zabezpieczeń i reguł zabezpieczeń oraz obowiązujących reguł zabezpieczeń. Korzystając z listy zastosowanych reguł, można określić, które porty są otwarte i które podlegają usterce.

### <a name="network-watcher"></a>Obserwator sieci

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie sieci w, do i z platformy Azure. Narzędzia do diagnostyki i wizualizacji sieci dostępne w Network Watcher pomagają zrozumieć, zdiagnozować i uzyskiwać wgląd w sieć na platformie Azure. Ta usługa obejmuje funkcję przechwytywania pakietów, następny przeskok, sprawdzenie przepływu IP, widok grupy zabezpieczeń, dzienniki przepływu sieciowej grupy zabezpieczeń. Monitorowanie poziomu scenariusza zapewnia kompleksowy wgląd w zasoby sieciowe w przeciwieństwie do monitorowania poszczególnych zasobów sieciowych.

### <a name="storage-analytics"></a>Analityka magazynu

[Analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) mogą przechowywać metryki, które obejmują zagregowane statystyki transakcji oraz dane pojemności dotyczące żądań do usługi magazynu. Transakcje są raportowane zarówno na poziomie operacji interfejsu API, jak i na poziomie usługi magazynu, a pojemność jest raportowana na poziomie usługi magazynu. Dane metryk umożliwiają analizowanie użycia usługi magazynu, diagnozowanie problemów z żądaniami dotyczącymi usługi Storage oraz Poprawianie wydajności aplikacji korzystających z usługi.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) to rozszerzalna usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web na wielu platformach. Użyj tej usługi do monitorowania aplikacji internetowej na żywo. Usługa automatycznie wykryje nieprawidłowości w zakresie wydajności. Zawiera ona zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów oraz zrozumienie, jakie użytkowników korzysta z Twojej aplikacji. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań. Działa w przypadku aplikacji na różnych platformach, w tym .NET, Node.js i Java EE, hostowanych lokalnie lub w chmurze. Integruje się z procesem devOps i ma punkty połączenia z różnymi narzędziami programistycznymi.

Monitoruje ona:

- **Liczby żądań, czasy reakcji i współczynniki błędów** — dowiedz się, które strony są najbardziej popularne, o jakiej porze dnia i gdzie są Twoi użytkownicy. Zobacz, które strony działają najlepiej. Jeśli Twoje czasy odpowiedzi i częstotliwości awarii są duże, gdy jest więcej żądań, być może masz problem z zasobami.

- **Współczynniki zależności, czasy reakcji i współczynniki błędów** — dowiedz się, czy usługi zewnętrzne nie spowalniają pracy.

- **Wyjątki** — analizowanie zagregowanych danych statystycznych lub wybieranie określonych wystąpień i przechodzenie do szczegółów śladu stosu i powiązanych żądań. Są zgłaszane zarówno wyjątki serwera, jak i przeglądarki.

- **Wydajność ładowania i wyświetleń stron** — zgłoszona przez przeglądarki użytkowników.

- **Wywołania AJAX ze stron sieci Web** — stawki, czasy odpowiedzi i wskaźniki niepowodzeń.

- **Liczba użytkowników i sesji.**

- **Liczniki wydajności** z serwerów systemu Windows lub Linux, takie jak użycie procesora CPU, pamięci i sieci.

- **Diagnostyka hosta** z platformy Docker lub Azure.

- **Diagnostyczne dzienniki śledzenia** z Twojej aplikacji — dzięki temu możesz skorelować zdarzenia śledzenia z żądaniami.

- **Niestandardowe zdarzenia i metryki** , które można napisać samodzielnie w kodzie klienta lub serwera, do śledzenia wydarzeń, takich jak sprzedane elementy lub gry.

Infrastruktura aplikacji zwykle obejmuje wiele składników — może to być maszyna wirtualna, konto magazynu i sieć wirtualna albo aplikacja internetowa, baza danych, serwer bazy danych i usługi zewnętrzne. Te składniki nie są widoczne jako osobne jednostki, tylko jako powiązane i zależne od siebie nawzajem części jednej całości. Dlatego najlepiej wdrażać i monitorować je oraz zarządzać nimi grupowo. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) umożliwia współpracę z zasobami w rozwiązaniu jako Grupa.

Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Wdrażanie wykonuje się przy użyciu szablonu, którego można następnie używać w różnych środowiskach (testowanie, etap przejściowy i produkcja). Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i tagowania ułatwiające zarządzanie zasobami po wdrożeniu.

**Zalety korzystania z usługi Resource Manager**

Usługa Resource Manager zapewnia kilka korzyści:

- Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).

- Możliwość wielokrotnego wdrażania rozwiązania w całym cyklu programistycznym z gwarancją spójnego stanu zasobów po każdym wdrożeniu.

- Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

- Można zdefiniować zależności między zasobami, aby zostały wdrożone w odpowiedniej kolejności.

- Możliwość stosowania kontroli dostępu do wszystkich usług w grupie zasobów dzięki natywnej integracji funkcji kontroli dostępu na podstawie ról z platformą zarządzania.

- Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.

- Możliwość wyjaśniania rozliczeń w organizacji przez wyświetlanie kosztów dla grupy zasobów korzystających z tego samego tagu.

> [!Note]
> Usługa Resource Manager udostępnia nową metodę wdrażania rozwiązań i zarządzania nimi. Jeśli używasz wcześniejszego modelu wdrażania i chcesz dowiedzieć się więcej o zmianach, zobacz [opis Menedżer zasobów wdrożenia i wdrożenia klasycznego](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zabezpieczeniach, odczytując niektóre szczegółowe tematy dotyczące zabezpieczeń:

- [Inspekcja i rejestrowanie](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cybercrime](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Bezpieczeństwo projektowania i działania](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Szyfrowanie](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Zarządzanie tożsamościami i dostępem](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Bezpieczeństwo sieci](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Zarządzanie zagrożeniami](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)

---
title: Podstawowa baza zabezpieczeń Azure dla pamięci podręcznej Azure HPC
description: Linia bazowa zabezpieczeń pamięci podręcznej platformy Azure HPC zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: dc695cc36113430cb1820d978ed41f5250cad33e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974900"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Podstawowa baza zabezpieczeń Azure dla pamięci podręcznej Azure HPC

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 2,0](../security/benchmarks/overview.md) do Microsoft Azure pamięci podręcznej HPC. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące pamięci podręcznej platformy Azure HPC. Wykluczenia **nie mają** zastosowania w przypadku pamięci podręcznej platformy Azure HPC.

Aby dowiedzieć się, jak pamięć podręczna Azure HPC jest całkowicie mapowana na test porównawczy zabezpieczeń Azure, zapoznaj się z [pełnym plikiem mapowania linii bazowej zabezpieczeń usługi Azure HPC cache](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [wzorzec zabezpieczeń Azure: zabezpieczenia sieci](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementowanie zabezpieczeń dla ruchu wewnętrznego

**Wskazówki**: podczas wdrażania zasobów pamięci podręcznej platformy Azure HPC należy utworzyć lub użyć istniejącej sieci wirtualnej. 

Upewnij się, że wszystkie sieci wirtualne platformy Azure są zgodne z zasadami segmentacji przedsiębiorstwa, które są dostosowane do ryzyka biznesowego. Każdy system, który może ponieść wyższy poziom ryzyka dla organizacji, powinien być odizolowany w ramach własnej sieci wirtualnej i dostatecznie zabezpieczony za pomocą sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i/lub zapory platformy Azure.

Aby można było korzystać z pamięci podręcznej, należy skonfigurować dwa wymagania wstępne związane z siecią: 

- Dedykowana podsieć dla wystąpienia pamięci podręcznej platformy Azure HPC

- Obsługa systemu DNS, aby pamięć podręczna mogła uzyskać dostęp do magazynu i innych zasobów

Pamięć podręczna Azure HPC wymaga dedykowanej podsieci z następującymi jakościami: 

- Podsieć musi mieć co najmniej 64 adresów IP.

- Podsieć nie może obsługiwać żadnych innych maszyn wirtualnych, nawet w przypadku powiązanych usług takich jak komputery klienckie.

- W przypadku korzystania z wielu wystąpień pamięci podręcznej platformy Azure HPC każda z nich musi mieć własną podsieć.

Najlepszym rozwiązaniem jest utworzenie nowej podsieci dla każdej pamięci podręcznej. W ramach tworzenia pamięci podręcznej można utworzyć nową sieć wirtualną i podsieć.

Aby można było korzystać z pamięci podręcznej HPC z lokalnym magazynem NAS, należy się upewnić, że niektóre porty w sieci lokalnej zezwalają na nieograniczony ruch z podsieci usługi Azure HPC. 

- [Jak skonfigurować porty dla dostępu do magazynu NFS](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [Jak utworzyć sieciową grupę zabezpieczeń z regułami zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Jak wdrożyć i skonfigurować zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="ns-2-connect-private-networks-together"></a>NS-2: łączenie sieci prywatnych razem

**Wskazówki**: Użyj usługi Azure ExpressRoute lub wirtualnej sieci prywatnej (VPN) platformy Azure do tworzenia prywatnych połączeń między centrami danych platformy Azure i infrastrukturą lokalną w środowisku z różnymi lokalizacjami. Połączenia ExpressRoute nie przechodzą przez publiczny Internet i oferują większą niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia z Internetem. W przypadku połączeń sieci VPN typu punkt-lokacja i sieci VPN typu lokacja-lokacja można połączyć lokalne urządzenia lub sieci z siecią wirtualną przy użyciu dowolnej kombinacji tych opcji sieci VPN i usługi Azure ExpressRoute. 

Aby połączyć co najmniej dwie sieci wirtualne na platformie Azure, użyj komunikacji równorzędnej sieci wirtualnej. Ruch sieciowy między równorzędnymi sieciami wirtualnymi jest prywatny i jest przechowywany w sieci szkieletowej platformy Azure.

- [Co to są modele łączności ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Omówienie usługi Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Ustanów dostęp do sieci prywatnej do usług platformy Azure

**Wskazówki**: Użyj punktów końcowych usługi Azure Virtual Network, aby zapewnić bezpieczny dostęp do pamięci podręcznej HPC. Punkty końcowe usługi są zoptymalizowaną trasą w sieci szkieletowej platformy Azure bez przekraczania Internetu. 

Pamięć podręczna HPC nie obsługuje korzystania z prywatnego linku platformy Azure do zabezpieczania punktów końcowych zarządzania do sieci prywatnej. 

Dostęp prywatny stanowi dodatkową miarę dotyczącą ochrony i zabezpieczeń ruchu oferowanej przez usługi platformy Azure.

- [Informacje o punktach końcowych usługi Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Informacje o sposobie instalowania pamięci podręcznej platformy Azure HPC](hpc-cache-mount.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrona aplikacji i usług przed atakami z sieci zewnętrznej

**Wskazówki**: Ochrona zasobów pamięci podręcznej HPC przed atakami z sieci zewnętrznych, w tym atakami typu "odmowa usługi" (DDoS), atakami specyficznymi dla aplikacji oraz nieproszonym i potencjalnie złośliwym ruchem internetowym. 

Platforma Azure oferuje natywne możliwości tej ochrony: 

- Używanie zapory platformy Azure do ochrony aplikacji i usług przed potencjalnie złośliwym ruchem z Internetu i innych lokalizacji zewnętrznych. 
- Chroń zasoby przed atakami DDoS, włączając ochronę standardową DDoS w sieciach wirtualnych platformy Azure. 
- Użyj Azure Security Center do wykrywania niepowodowanych problemów z konfiguracją zasobów sieciowych.

Pamięć podręczna platformy Azure HPC nie jest przeznaczona do uruchamiania aplikacji sieci Web i nie wymaga konfigurowania żadnych dodatkowych ustawień ani wdrażania dodatkowych usług sieciowych do ochrony przed atakami z sieci zewnętrznych, które są przeznaczone dla aplikacji sieci Web.

- [Dokumentacja zapory platformy Azure](/azure/firewall/) 

- [Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Rekomendacje usługi Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: wdrażanie systemów zapobiegania wykrywaniu/włamaniom (identyfikatorów/adresów IP)

**Wskazówki**: Używanie zapory platformy Azure z filtrowaniem opartym na analizie zagrożeń w celu wysyłania alertów dotyczących i/lub blokowania ruchu do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. 

Gdy jest wymagana inspekcja ładunku, można wdrożyć rozwiązanie do wykrywania i zapobiegania włamaniom innych firm w witrynie Azure Marketplace z możliwościami inspekcji ładunku. Alternatywnie możesz wybrać użycie identyfikatorów/adresów IP opartych na hoście lub rozwiązania do wykrywania punktów końcowych (EDR) opartych na hoście w połączeniu z lub zamiast identyfikatorów/adresów IP opartych na sieci.

Uwaga: Jeśli posiadasz przepisy prawne lub inne wymagania dotyczące używania identyfikatorów/adresów IP, upewnij się, że jest zawsze dostrojona, aby zapewnić wysoką jakość alertów w rozwiązaniu SIEM.

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [Możliwości identyfikatorów innych firm w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Funkcja Microsoft Defender ATP EDR](/windows/security/threat-protection/microsoft-defender-atp/overviewendpoint-detection-response)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: uproszczenie reguł zabezpieczeń sieci

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla ofert, które można wdrożyć w sieciach wirtualnych platformy Azure, lub możliwość definiowania grupowania dozwolonych zakresów adresów IP w celu wydajnego zarządzania. Pamięć podręczna HPC nie obsługuje obecnie tagów usługi. 

Najlepszym rozwiązaniem jest utworzenie nowej podsieci dla każdej pamięci podręcznej. W ramach tworzenia pamięci podręcznej można utworzyć nową sieć wirtualną i podsieć.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: bezpieczna usługa nazw domen (DNS)

**Wskazówki**: Postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń systemu DNS, aby zmniejszyć ryzyko związane z typowymi atakami, takimi jak zawieszonego DNS, DNS wzmocnienie ataków, zatrucie i fałszowanie DNS oraz inne.

Pamięć podręczna Azure HPC potrzebuje usługi DNS, aby uzyskać dostęp do zasobów poza prywatną siecią wirtualną pamięci podręcznej. Jeśli przepływ pracy zawiera zasoby spoza platformy Azure, musisz skonfigurować i zabezpieczyć własny serwer DNS oprócz korzystania z Azure DNS.

- Aby uzyskać dostęp do punktów końcowych usługi Azure Blob Storage, komputerów klienckich opartych na platformie Azure lub innych zasobów platformy Azure, użyj Azure DNS.
- Aby uzyskać dostęp do magazynu lokalnego lub połączyć się z pamięcią podręczną od klientów spoza platformy Azure, należy utworzyć niestandardowy serwer DNS, który może rozpoznać te nazwy hosta.
- Jeśli przepływ pracy zawiera zasoby wewnętrzne i zewnętrzne, skonfiguruj niestandardowy serwer DNS, aby przekazywać do serwera Azure DNS żądania rozpoznawania specyficzne dla platformy Azure. 

Gdy Azure DNS jest używany jako autorytatywna usługa DNS, należy się upewnić, że strefy i rekordy DNS są chronione przed przypadkowym lub złośliwym modyfikacją przy użyciu funkcji RBAC i blokad zasobów platformy Azure.

W przypadku konfigurowania własnego serwera DNS należy przestrzegać następujących wytycznych dotyczących zabezpieczeń:

- [Przewodnik wdrażania bezpiecznego systemu nazw domen (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Zapobiegaj zawieszonego wpisów DNS i unikaj przejęcia domen podrzędnych](../security/fundamentals/subdomain-takeover.md) 

- [Przeczytaj więcej na temat wymagań dostępu DNS dla pamięci podręcznej HPC](hpc-cache-prerequisites.md#dns-access)

- [Przegląd Azure DNS](../dns/dns-overview.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>Wiadomość błyskawiczna-1: standaryzacja Azure Active Directory jako centralny system tożsamości i uwierzytelniania

**Wskazówki**: pamięć podręczna Azure HPC nie jest zintegrowana z Azure Active Directory dla operacji wewnętrznych. Usługa Azure AD może być jednak używana do uwierzytelniania użytkowników w Azure Portal lub interfejsie wiersza polecenia w celu tworzenia, wyświetlania i zarządzania wdrożeniami pamięci podręcznej HPC oraz pokrewnymi składnikami.

Azure Active Directory (Azure AD) to domyślna usługa zarządzania tożsamościami i dostępem na platformie Azure. Należy przeprowadzić standaryzację usługi Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie:

- Microsoft Cloud zasoby, takie jak Azure Portal, Azure Storage, Azure Virtual Machine (Linux i Windows), Azure Key Vault, PaaS i SaaS.

- Zasoby organizacji, takie jak aplikacje na platformie Azure lub zasoby sieci firmowej.

Zabezpieczanie usługi Azure AD powinno mieć wysoki priorytet w zakresie zabezpieczeń w chmurze w organizacji. Usługa Azure AD zapewnia bezpieczną ocenę tożsamości, która pomaga ocenić stan zabezpieczeń tożsamości w porównaniu z zaleceniami dotyczącymi najlepszych rozwiązań firmy Microsoft. Skorzystaj z oceny, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań, i ulepszać stan zabezpieczeń.

Uwaga: usługa Azure AD obsługuje tożsamość zewnętrzną, która umożliwia użytkownikom bez konto Microsoft logowania się do aplikacji i zasobów przy użyciu tożsamości zewnętrznej.

- [Dzierżawa w Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Korzystanie z zewnętrznych dostawców tożsamości dla aplikacji](/azure/active-directory/b2b/identity-providers) 

- [Co to jest bezpieczna ocena tożsamości w Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>Wiadomości błyskawiczne — 2: Zarządzaj tożsamościami aplikacji bezpiecznie i automatycznie

**Wskazówki**: pamięć podręczna HPC używa tożsamości zarządzanych przez platformę Azure dla kont nieludzkich, takich jak usługi lub Automatyzacja. Zalecane jest używanie funkcji tożsamości zarządzanej platformy Azure zamiast tworzenia bardziej zaawansowanego konta ludzkiego w celu uzyskania dostępu do zasobów lub ich wykonania. 

Pamięć podręczna HPC może natywnie uwierzytelniać usługi platformy Azure/zasoby, które obsługują uwierzytelnianie usługi Azure AD za pomocą wstępnie zdefiniowanych reguł przyznawania dostępu. Pozwala to uniknąć konieczności używania zakodowanych poświadczeń w kodzie źródłowym lub w plikach konfiguracji.

- [Tożsamości zarządzane przez platformę Azure](../active-directory/managed-identities-azure-resources/overview.md) 

- [Usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>KOMUNIKATOR-3: używanie logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: pamięć podręczna Azure HPC nie jest zintegrowana z usługą Azure AD na potrzeby operacji wewnętrznych. Usługa Azure AD może być jednak używana do uwierzytelniania użytkowników w Azure Portal lub interfejsie wiersza polecenia w celu tworzenia, wyświetlania i zarządzania wdrożeniami pamięci podręcznej HPC oraz pokrewnymi składnikami.

Azure Active Directory zapewnia zarządzanie tożsamościami i dostępem do zasobów platformy Azure, aplikacji w chmurze i aplikacji lokalnych. Obejmuje to tożsamości przedsiębiorstwa, takie jak pracownicy, a także tożsamości zewnętrzne, takie jak partnerzy, dostawcy i dostawcy. Umożliwia to Logowanie jednokrotne (SSO) w celu zarządzania i bezpiecznego dostępu do danych i zasobów organizacji w środowisku lokalnym i w chmurze. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu i lepszego wglądu i kontroli.

- [Informacje na temat logowania jednokrotnego aplikacji za pomocą usługi Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>KOMUNIKATOR-4: Użyj kontrolek silnego uwierzytelniania dla wszystkich Azure Active Directory na podstawie dostępu

**Wskazówki**: Mimo że pamięć podręczna platformy Azure HPC nie jest zintegrowana z usługą Azure AD dla operacji wewnętrznych, można użyć usługi Azure AD do uwierzytelniania użytkowników w Azure Portal lub interfejsie wiersza polecenia w celu tworzenia, wyświetlania i zarządzania wdrożeniami pamięci podręcznej HPC oraz pokrewnymi składnikami.  

Usługa Azure AD obsługuje mechanizmy kontroli silnego uwierzytelniania za pomocą uwierzytelniania wieloskładnikowego (MFA) i silnych metod bezhaseł.

- Uwierzytelnianie wieloskładnikowe: Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami dotyczącymi zarządzania tożsamościami i dostępem Azure Security Center, aby zapoznać się z najlepszymi rozwiązaniami w konfiguracji usługi MFA. Usługę MFA można wymusić dla wszystkich użytkowników, wybrać użytkowników lub na poziomie poszczególnych użytkowników na podstawie warunków logowania i czynników ryzyka.
- Uwierzytelnianie bezhasło — dostępne są trzy opcje uwierzytelniania bezhasła: funkcja Windows Hello dla firm, aplikacja Microsoft Authenticator i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku administratorów i uprzywilejowanych użytkowników upewnij się, że używasz najwyższego poziomu metody silnego uwierzytelniania. Należy wdrożyć odpowiednie zasady silnego uwierzytelniania dla innych użytkowników.

Pamięć podręczna Azure HPC obsługuje również starsze uwierzytelnianie oparte na hasłach dla systemów klienckich, które łączą się z pamięcią podręczną. Te rodzaje połączeń obejmują konta tylko w chmurze (konta użytkowników utworzone bezpośrednio na platformie Azure), które mają zasady haseł linii bazowej lub konta hybrydowe (konta użytkowników, które pochodzą z lokalnego Active Directory), które będą zgodne z lokalnymi zasadami haseł. 

W przypadku korzystania z uwierzytelniania opartego na hasłach usługa Azure AD zapewnia funkcję ochrony hasłem, która uniemożliwia użytkownikom ustawianie haseł, które są łatwe do odgadnięcia. Firma Microsoft oferuje globalną listę zakazanych haseł, które są aktualizowane na podstawie danych telemetrycznych, a klienci mogą rozszerzyć listę w zależności od potrzeb (na przykład z marką, literaturą kulturową itp.). Ta ochrona hasłem może być używana w przypadku kont tylko w chmurze i hybrydowych.

Uwaga: uwierzytelnianie oparte na poświadczeniach hasła jest podatne na popularne metody ataków. Aby zapewnić wyższy poziom zabezpieczeń, należy użyć silnego uwierzytelniania, takiego jak MFA i zasad silnych haseł. Jeśli używasz aplikacji innych firm i usług portalu Marketplace z domyślnymi hasłami, Ustaw nowe, bezpieczne hasło przy pierwszym skonfigurowaniu usługi. 

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Wprowadzenie do opcji uwierzytelniania bezhasła dla Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Domyślne zasady haseł usługi Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminowanie nieprawidłowych haseł przy użyciu ochrony hasłem usługi Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>KOMUNIKATOR-5: Monitor i alert dotyczący anomalii kont

**Wskazówki**: pamięć podręczna Azure HPC może używać Azure Active Directory do zarządzania użytkownikami z Azure Portal.  Azure Active Directory udostępnia następujące źródła danych:

- Logowania — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.

- Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

- Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Te źródła danych można zintegrować z Azure Monitor, wskaźnikiem produktów platformy Azure lub systemami SIEM innych firm.

Azure Security Center może również otrzymywać alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia oraz na przestarzałe konta w subskrypcji.

Usługa Azure Advanced Threat Protection (ATP) to rozwiązanie zabezpieczeń, które może używać sygnałów Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, złamanych tożsamości i złośliwych działań niejawnych.

- [Raporty dotyczące inspekcji w programie Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Jak wyświetlić ryzykowne logowania usługi Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych w celu działania ryzykownego](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](../security-center/security-center-identity-access.md)

 

- [Alerty w Azure Security Center module ochrony przed zagrożeniami](../security-center/alerts-reference.md) 

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="im-7-eliminate-unintended-credential-exposure"></a>Wiadomości błyskawiczne — 7: eliminowanie niezamierzonego narażenia na poświadczenia

**Wskazówki**: nie dotyczy; Pamięć podręczna HPC nie pozwala klientom na wdrażanie żadnych utrwalonych danych w uruchomionym środowisku.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="privileged-access"></a>Uprzywilejowany dostęp

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: ograniczanie dostępu administracyjnego do systemów o krytycznym znaczeniu dla firmy

**Wskazówki**: pamięć podręczna HPC korzysta z usługi Azure RBAC, aby odizolować dostęp do systemów o krytycznym znaczeniu dla firmy przez ograniczenie kont, które mają przyznane uprzywilejowany dostęp do subskrypcji i grup zarządzania, do których się znajdują.

Tworzenie standardowych procedur operacyjnych wokół korzystania z dedykowanych kont administracyjnych. W celu utworzenia pamięci podręcznej pamięć podręczna HPC wymaga od użytkowników posiadania wystarczających uprawnień w ramach subskrypcji w celu utworzenia kart sieciowych. W przypadku korzystania z usługi BLOB Storage Współautorzy kont usługi roli RBAC oraz współautor danych obiektów blob magazynu są wymagane dla pamięci podręcznej HPC w celu uzyskania dostępu do magazynu. 

Upewnij się, że ograniczono również dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do zasobów o znaczeniu krytycznym dla firmy, takich jak kontrolery domena usługi Active Directory (DC), narzędzia zabezpieczeń i narzędzia do zarządzania systemem z agentami zainstalowanymi w systemach krytycznych dla firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia poziomu zasobów krytycznych dla firmy.

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu.

- [Uprawnienia kontroli RBAC pamięci podręcznej platformy Azure HPC](hpc-cache-prerequisites.md#permissions)

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Dostęp do grupy zarządzania](../governance/management-groups/overview.md#management-group-access)

- [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: przegląd i regularne uzgadnianie dostępu użytkowników

**Wskazówki**: regularnie Przeglądaj konta użytkowników i przypisywanie dostępu, aby upewnić się, że konta i ich poziomy dostępu są prawidłowe. 

Pamięć podręczna Azure HPC umożliwia korzystanie z kont usługi Azure Active Directory (Azure AD) w celu zarządzania dostępem użytkowników za pomocą Azure Portal i powiązanych interfejsów. Usługa Azure AD oferuje przeglądy dostępu, które ułatwiają przeglądanie członkostw w grupach, dostęp do aplikacji dla przedsiębiorstw i przypisań ról. Raportowanie usługi Azure AD może zapewnić dzienniki, aby ułatwić odnajdywanie starych kont. Możesz również użyć Azure AD Privileged Identity Management, aby utworzyć przepływ pracy raportu przeglądu dostępu, aby ułatwić proces recenzowania.

Ponadto usługa Azure Privileged Identity Management może być skonfigurowana do zgłaszania alertów w przypadku tworzenia zbyt dużej liczby kont administratorów oraz do identyfikowania kont administratorów, które są przestarzałe lub nieprawidłowo skonfigurowane.

Uwaga: Niektóre usługi platformy Azure obsługują lokalnych użytkowników i role, które nie są zarządzane za pomocą usługi Azure AD. Trzeba będzie osobno zarządzać tymi użytkownikami.

W przypadku korzystania z obiektów docelowych magazynu NFS należy skontaktować się z administratorami sieci i menedżerami zapory, aby sprawdzić ustawienia dostępu i upewnić się, że pamięć podręczna Azure HPC będzie mogła komunikować się z systemami magazynu NFS.

- [Listę uprawnień pamięci podręcznej HPC można znaleźć w sekcji wymagania wstępne dotyczące pamięci podręcznej platformy Azure HPC](hpc-cache-prerequisites.md) 

- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure w Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurowanie dostępu awaryjnego w usłudze Azure AD

**Wskazówki**: pamięć podręczna HPC może używać Azure Active Directory do zarządzania dostępem do zasobów za pomocą Azure Portal. Aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure AD, skonfiguruj konto dostępu awaryjnego w celu uzyskania dostępu, gdy nie można używać normalnych kont administracyjnych. Konta dostępu awaryjnego są zwykle wysoce uprzywilejowane i nie powinny być przypisane do konkretnych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub "szkła Break", w których normalne konta administracyjne nie mogą być używane.

Należy upewnić się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do ich używania tylko w sytuacji awaryjnej.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatyzowanie zarządzania uprawnieniami 

**Wskazówki**: pamięć podręczna HPC może używać Azure Active Directory do zarządzania dostępem do zasobów pamięci podręcznej za pomocą Azure Portal. 

Użyj funkcji zarządzania prawami usługi Azure AD, aby zautomatyzować przepływy pracy żądania dostępu, w tym przydziały dostępu, przeglądy i wygaśnięcie. Obsługiwane jest również podwójne lub wieloetapowe zatwierdzenie. 

- [Co to są przeglądy dostępu do usługi Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Co to jest zarządzanie prawami w usłudze Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: przestrzeganie wystarczającej liczby czynności administracyjnych (najmniejsza zasada uprawnień) 

**Wskazówki**: pamięć podręczna HPC jest zintegrowana z kontrolą dostępu opartą na ROLACH (RBAC) na platformie Azure w celu zarządzania swoimi zasobami. Usługa Azure RBAC umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal. 

Uprawnienia przypisane do zasobów za pomocą usługi Azure RBAC powinny być zawsze ograniczone do tego, co jest wymagane przez role. To uzupełnia podejście just-in-Time (JIT) Azure AD Privileged Identity Management (PIM) i powinno być okresowo weryfikowane.

Przy użyciu wbudowanych ról można przydzielić uprawnienia i w razie potrzeby utworzyć tylko rolę niestandardową.

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md) 

- [Jak skonfigurować RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: odnajdywanie, klasyfikowanie i etykietowanie danych poufnych 

**Wskazówki**: pamięć podręczna HPC zarządza danymi poufnymi, ale nie ma możliwości odnajdywania, klasyfikowania i etykietowania poufnych danych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrona poufnych danych

**Wskazówki**: Ochrona poufnych danych przez ograniczenie dostępu przy użyciu Access Control opartej na rolach (Azure RBAC), kontroli dostępu opartej na sieci i określonych kontrolek w usługach platformy Azure (takich jak szyfrowanie w SQL i innych bazach danych).

Aby zapewnić spójną kontrolę dostępu, wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa. Strategię segmentacji przedsiębiorstwa należy również uzyskać informacje o lokalizacji poufnych lub krytycznych danych i systemów.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i chroniącą przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft zaimplementował pewne domyślne funkcje i możliwości ochrony danych.

- [Access Control oparte na rolach (RBAC) na platformie Azure](../role-based-access-control/overview.md) 

- [Zrozumienie ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitoruj nieautoryzowane transfery danych poufnych

**Wskazówki**: pamięć podręczna HPC przesyła dane poufne, ale nie obsługuje monitorowania nieautoryzowanego transferu poufnych danych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfruj poufne informacje podczas przesyłania

**Wskazówki**: pamięć podręczna HPC obsługuje szyfrowanie danych podczas przesyłania przy użyciu protokołu TLS w wersji 1.2 lub nowszej.

Chociaż jest to opcjonalne dla ruchu w sieciach prywatnych, ma to kluczowe znaczenie dla ruchu w sieciach zewnętrznych i publicznych. W przypadku ruchu HTTP upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS w wersji 1.2 lub nowszej. W celu zarządzania zdalnego należy użyć protokołu SSH (dla systemu Linux) lub protokołu RDP/TLS (w systemie Windows), a nie z nieszyfrowanym protokołem. Przestarzałe protokoły SSL, TLS i SSH oraz słabe szyfry powinny być wyłączone.

Domyślnie platforma Azure zapewnia szyfrowanie danych przesyłanych między centrami danych platformy Azure.

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informacje o zabezpieczeniach protokołu TLS](/security/engineering/solving-tls1-problem) 

- [Podwójne szyfrowanie danych na platformie Azure podczas przesyłania](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Szyfruj poufne dane w spoczynku

**Wskazówki**: aby uzupełnić kontrolę dostępu, dane przechowywane w spoczynku powinny być chronione przed atakami "poza pasmem" (na przykład uzyskiwania dostępu do magazynu bazowego) przy użyciu szyfrowania. Pozwala to zagwarantować, że osoby atakujące nie mogą łatwo odczytywać ani modyfikować danych.

Domyślnie platforma Azure zapewnia szyfrowanie danych. W przypadku wysoce poufnych danych masz możliwość zaimplementowania dodatkowego szyfrowania dla wszystkich zasobów platformy Azure, jeśli jest to możliwe. Platforma Azure domyślnie zarządza kluczami szyfrowania, ale platforma Azure udostępnia opcje zarządzania własnymi kluczami (klucze zarządzane przez klienta) dla niektórych usług platformy Azure.

Wszystkie dane przechowywane na platformie Azure, w tym na dyskach pamięci podręcznej, są szyfrowane domyślnie przy użyciu kluczy zarządzanych przez firmę Microsoft. Ustawienia pamięci podręcznej platformy Azure HPC należy dostosować tylko wtedy, gdy chcesz zarządzać kluczami używanymi do szyfrowania danych.

Jeśli jest to wymagane na potrzeby zgodności zasobów obliczeniowych, zaimplementuj narzędzie innej firmy, takie jak automatyczne rozwiązanie do ochrony przed utratą danych oparte na hoście, aby wymusić kontrolę dostępu do danych nawet wtedy, gdy dane są kopiowane poza system.

- [Jak używać kluczy szyfrowania zarządzanych przez klienta z użyciem pamięci podręcznej platformy Azure HPC](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-create?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Informacje o szyfrowaniu na platformie Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Jak skonfigurować zarządzane przez klienta klucze szyfrowania](/azure/storage/common/storage-encryption-keys-portal) 

- [Model szyfrowania i tabela zarządzania kluczami](../security/fundamentals/encryption-atrest.md)

 

- [Szyfrowanie danych przy podwójnej obspoczynku na platformie Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: zarządzanie zasobami](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu Azure Security Center. 

W zależności od tego, jak są strukturalne obowiązki zespołu zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu zabezpieczeń lub zespołu lokalnego. Wspomniane powyżej informacje o zabezpieczeniach i zagrożeniach muszą zawsze zostać zagregowane w organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (głównej grupy zarządzania) lub zakresu do grup zarządzania lub określonych subskrypcji. 

Uwaga: w celu uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnik zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie Grupy zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu zasobów i metadanych

**Wskazówki**: pamięć podręczna Azure HPC obsługuje używanie tagów. Zastosuj Tagi do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. 

Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym. Tagi można dodawać podczas tworzenia pamięci podręcznej, a także po wdrożeniu pamięci podręcznej. 

Użyj spisu maszyn wirtualnych platformy Azure, aby zautomatyzować zbieranie informacji o oprogramowaniu na Virtual Machines. W Azure Portal są dostępne nazwy, wersje, Wydawca i czas odświeżania oprogramowania. Aby uzyskać dostęp do daty instalacji i innych informacji, Włącz diagnostykę na poziomie gościa i Przenieś dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.
Pamięć podręczna HPC nie zezwala na uruchamianie aplikacji ani Instalowanie oprogramowania w swoich zasobach. 

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center zarządzanie spisem zasobów](../security-center/asset-inventory.md) 

- [Przewodnik po decyzjach dotyczących nazewnictwa i tagowania zasobów](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json) 

- [Jak włączyć Spis maszyn wirtualnych platformy Azure](../automation/automation-tutorial-installed-software.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: pamięć podręczna HPC obsługuje wdrożenia Azure Resource Manager. Użyj Azure Policy, aby przeprowadzić inspekcję i ograniczyć liczbę usług, które użytkownicy mogą inicjować w danym środowisku. Użyj grafu zasobów platformy Azure do wykonywania zapytań dotyczących zasobów i odnajdywania ich w ramach subskrypcji. Za pomocą Azure Monitor można tworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi.

- [Konfigurowanie Azure Policy i zarządzanie nimi](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types) 

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zapewnij bezpieczeństwo zarządzania cyklem życia zasobów

**Wskazówki**: nie dotyczy. Nie można użyć pamięci podręcznej platformy Azure HPC do zapewnienia bezpieczeństwa zasobów w procesie zarządzania cyklem życia. Klient jest odpowiedzialny za zachowanie atrybutów i konfiguracji sieci zasobów, które są uważane za duże znaczenie. 

Zaleca się, aby klient utworzył proces do przechwytywania zmian konfiguracji atrybutów i sieci, pomiaru wpływu zmiany i tworzenia zadań korygowania stosownie do potrzeb.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Włączanie wykrywania zagrożeń dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanej funkcji wykrywania zagrożeń Azure Security Center i Włącz usługę Azure Defender (na przykład zaawansowanej ochrony przed zagrożeniami na platformie Azure) dla zasobów pamięci podręcznej HPC. Usługa Azure Defender dla pamięci podręcznej programu HPC oferuje dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do zasobów pamięci podręcznej lub korzystania z nich.

Przekazuj wszystkie dzienniki z pamięci podręcznej HPC do SIEM, których można użyć do skonfigurowania niestandardowych wykryć zagrożeń. Upewnij się, że monitorujesz różne typy zasobów platformy Azure pod kątem potencjalnych zagrożeń i anomalii. Skup się na uzyskiwaniu alertów o wysokiej jakości, aby zmniejszyć liczbę fałszywych dodatnich dla analityków do sortowania. Alerty mogą być źródłem danych dziennika, agentów lub innych danych.

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection) 

- [Przewodnik dotyczący podręcznika Azure Security Center alertów zabezpieczeń](../security-center/alerts-reference.md) 

- [Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń](../sentinel/tutorial-detect-threats-custom.md) 

- [Analiza zagrożeń cybernetycznymi z użyciem platformy Azure](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Włączanie wykrywania zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: usługa Azure AD udostępnia następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, wskaźnikiem na platformie Azure lub innymi narzędziami Siem/monitorowania w celu uzyskania bardziej zaawansowanych przypadków użycia monitorowania i analiz:
- Logowania — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.

- Dzienniki inspekcji — zapewniają możliwość śledzenia za pomocą dzienników dla wszystkich zmian wykonywanych przez różne funkcje w ramach usługi Azure AD. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

- Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

- Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Azure Security Center może również otrzymywać alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia lub przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń Azure Security Center moduł ochrony przed zagrożeniami może również zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (SQL DB i magazynu) oraz warstw usług platformy Azure. Ta funkcja umożliwia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w programie Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Włącz usługę Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania dla aktywności sieci platformy Azure

**Wskazówki**: można używać bram sieci VPN i ich funkcji przechwytywania pakietów oprócz powszechnie dostępnych narzędzi do przechwytywania pakietów, aby rejestrować pakiety sieciowe przesyłane między sieciami wirtualnymi.

Wdróż sieciową grupę zabezpieczeń w sieci, w której wdrożone są zasoby pamięci podręcznej platformy Azure HPC. Włącz dzienniki przepływu sieciowych grup zabezpieczeń w sieciowych grupach zabezpieczeń na potrzeby inspekcji ruchu.

Dzienniki przepływów są przechowywane na koncie magazynu. Włącz rozwiązanie Analiza ruchu, aby przetwarzać i wysyłać te dzienniki przepływów do Log Analytics obszaru roboczego. Analiza ruchu zapewnia dodatkowe informacje o przepływie ruchu dla sieci platformy Azure. Analiza ruchu może pomóc w wizualizacji aktywności sieciowej i zidentyfikowaniu aktywnych punktów, zidentyfikowaniu zagrożeń bezpieczeństwa, zrozumieniu wzorców przepływu ruchu oraz wyróżnieniu konfiguracji sieci.

Pamięć podręczna potrzebuje systemu DNS, aby uzyskać dostęp do zasobów poza jego siecią wirtualną. W zależności od zasobów, z których korzystasz, może być konieczne skonfigurowanie niestandardowego serwera DNS i skonfigurowanie przesyłania dalej między tym serwerem a serwerami Azure DNS. 

Zaimplementuj rozwiązanie innych firm z witryny Azure Marketplace na potrzeby rejestrowania w systemie DNS, zgodnie z potrzebami organizacji.

- [Konfigurowanie przechwytywania pakietów dla bram sieci VPN](../vpn-gateway/packet-capture.md) 

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md) 

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md) 

- [Dowiedz się więcej o wymaganiach wstępnych dotyczących systemu DNS](hpc-cache-prerequisites.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włączanie rejestrowania dla zasobów platformy Azure

**Wskazówki**: zasoby pamięci podręcznej platformy Azure HPC automatycznie tworzą dzienniki aktywności. Te dzienniki zawierają wszystkie operacje zapisu (PUT, POST, DELETE), ale nie obejmują operacji odczytu (GET). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

Można również użyć Azure Security Center i Azure Policy do włączenia dzienników zasobów platformy Azure dla pamięci podręcznej HPC oraz zbierania danych i rejestrowania ich. Te dzienniki mogą być niezbędne do późniejszego badania zdarzeń związanych z bezpieczeństwem i wykonywania ćwiczeń śledczej.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Omówienie zbierania danych Azure Security Center](../security-center/security-center-enable-data-collection.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: udostępnione

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: scentralizowany magazyn rejestrowania i analiza w celu włączenia korelacji. Dla każdego źródła dziennika upewnij się, że masz przypisany właściciel danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia używane do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych.

Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnym logowaniem. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: odpowiedź na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: przygotowanie — aktualizacja procesu reagowania na incydenty dla platformy Azure

**Wskazówki**: Upewnij się, że organizacja ma procesy odpowiadające na zdarzenia związane z bezpieczeństwem, Zaktualizowano te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik odwołuje się do odpowiedzi na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: przygotowanie — Skonfiguruj powiadomienie o zdarzeniu 

**Wskazówki**: Konfigurowanie informacji kontaktowych dotyczących zdarzeń zabezpieczeń w Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nielegalną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: wykrywanie i analiza — Tworzenie zdarzeń na podstawie alertów o wysokiej jakości

**Wskazówki**: Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia jakości alertów. Dzięki temu można uczyć się lekcji od przeszłych zdarzeń i określić priorytety alertów dla analityków, dzięki czemu nie będą one tracić czasu na fałszywie dodatnich. 

Alerty o wysokiej jakości można kompilować na podstawie doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności i narzędzi przeznaczonych do generowania i czyszczenia alertów przez odmowę i skorelowanie różnorodnych źródeł sygnałów. 

Azure Security Center oferuje alerty wysokiej jakości dla wielu zasobów platformy Azure. Łącznika danych ASC można użyć do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. Wskaźnik oceny platformy Azure umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia dotyczące Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały sposób ciągły.

- [Jak skonfigurować eksportowanie](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../sentinel/connect-azure-security-center.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: wykrywanie i analiza — Zbadaj zdarzenie

**Wskazówki**: Upewnij się, że analitycy mogą wykonywać zapytania i używać różnorodnych źródeł danych podczas badania potencjalnych zdarzeń, aby zbudować pełny wgląd w to, co się stało. Należy zebrać różne dzienniki, aby śledzić działania potencjalnej osoby atakującej w łańcuchu kasowania, aby uniknąć niewidomych plamek.  Należy również upewnić się, że szczegółowe informacje i uczenia są przechwytywane dla innych analityków i w przyszłości.  

Źródła danych do zbadania obejmują scentralizowane źródła rejestrowania, które są już zbierane z usług w zakresie i działających systemów, ale mogą również obejmować:

- Dane sieci — używanie dzienników przepływów sieciowych grup zabezpieczeń, platformy Azure Network Watcher i Azure Monitor do przechwytywania dzienników przepływu sieci i innych informacji analitycznych. 

- Migawki uruchomionych systemów: 

    - Użyj funkcji migawek maszyny wirtualnej platformy Azure, aby utworzyć migawkę dysku działającego systemu. 

    - Użyj możliwości natywnego zrzutu pamięci systemu operacyjnego, aby utworzyć migawkę pamięci uruchomionego systemu.

    - Użyj funkcji Snapshot w ramach usług platformy Azure lub własnej funkcji oprogramowania, aby utworzyć migawki uruchomionych systemów.

Platforma Azure — wskaźnik przedstawia szeroką analizę danych w praktycznie dowolnym źródle dziennika i portalu zarządzania przypadkami do zarządzania pełnym cyklem życia zdarzeń. Informacje o analizie podczas badania mogą być powiązane ze zdarzeniami w celu śledzenia i raportowania. 

- [Tworzenie migawek dysku maszyny z systemem Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Tworzenie migawek dysku maszyny z systemem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure obsługi informacji diagnostycznych i kolekcji zrzutów pamięci](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Zbadaj zdarzenia za pomocą platformy Azure — wskaźnik](../sentinel/tutorial-investigate-cases.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki**: Zapewnij kontekst analitykom, na których zdarzenia mają być skoncentrowane na początku, na podstawie ważności alertów i poufności zasobów. 

Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznie używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar za działanie, które spowodowało wygenerowanie alertu.

Ponadto należy oznaczyć zasoby przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: zawieranie, likwidowanie i odzyskiwanie — Automatyzacja obsługi zdarzeń

**Wskazówki**: automatyzuje ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę incydentów, które może obsłużyć analityk. Zadania ręczne zwiększają również zmęczenie analityków, które zwiększają ryzyko wystąpienia błędu ludzkiego powodującego opóźnienia i obniżają możliwości skoncentrowania się na skomplikowanych zadaniach analityków. Korzystając z funkcji automatyzacji przepływu pracy w Azure Security Center i wskaźnik platformy Azure, można automatycznie wyzwalać akcje lub uruchamiać element PlayBook w celu reagowania na przychodzące alerty zabezpieczeń. Element PlayBook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w Security Center](../security-center/workflow-automation.md)

- [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia w Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia na platformie Azure — wskaźnik](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="posture-and-vulnerability-management"></a>Stan i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>WA-3: Ustanów bezpieczne konfiguracje dla zasobów obliczeniowych

**Wskazówki**: Użyj Azure Security Center i Azure Policy do ustanowienia bezpiecznych konfiguracji dla wszystkich zasobów obliczeniowych, w tym maszyn wirtualnych, kontenerów i innych.

- [Jak monitorować zalecenia dotyczące Azure Security Center](../security-center/security-center-recommendations.md) 

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../security-center/recommendations-reference.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: przeprowadzenie regularnej symulacji ataków

**Wskazówki**: w razie potrzeby przeprowadź testowanie penetracji lub czerwone działania zespołu na Twoich zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z Microsoft Cloud regułami testowania penetracji, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

- [Testowanie penetracji na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Tworzenie zespołu Red.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="backup-and-recovery"></a>Kopia zapasowa i odzyskiwanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test — tworzenie kopii zapasowych i odzyskiwanie](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zapewnianie zwykłych zautomatyzowanych kopii zapasowych

**Wskazówki**: ponieważ pamięć podręczna platformy Azure HPC to rozwiązanie pamięci podręcznej, a nie system magazynu, należy skoncentrować się na zapewnieniu regularnego tworzenia kopii zapasowych danych w jego celach magazynowych. Postępuj zgodnie ze standardowymi procedurami kontenerów obiektów blob platformy Azure oraz tworzeniem kopii zapasowych lokalnych miejsc do magazynowania. 

Aby zminimalizować zakłócenia w przypadku awarii regionalnej, można wykonać kroki w celu zapewnienia dostępu do danych między regionami.  

Każde wystąpienie pamięci podręcznej platformy Azure HPC działa w ramach określonej subskrypcji i w jednym regionie. Oznacza to, że przepływ pracy pamięci podręcznej może być prawdopodobnie zakłócony, jeśli region ma pełną awarię. Aby zminimalizować to zakłócenia, organizacja powinna używać magazynu zaplecza, który jest dostępny z wielu regionów. Ten magazyn może być lokalnym systemem NAS z odpowiednią obsługą systemu DNS lub magazynem obiektów blob platformy Azure, który znajduje się w innym regionie niż pamięć podręczna.

Gdy przepływ pracy zostanie przeprowadzony w regionie podstawowym, dane są zapisywane w magazynie długoterminowym poza regionem. Jeśli region pamięci podręcznej stanie się niedostępny, możesz utworzyć zduplikowane wystąpienie pamięci podręcznej platformy Azure HPC w regionie pomocniczym, nawiązać połączenie z tym samym magazynem i wznowić pracę z nowej pamięci podręcznej.

- [Przeczytaj więcej na temat regionalnej pracy w trybie failover](hpc-region-recovery.md)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="br-2-encrypt-backup-data"></a>BR-2: Szyfruj dane kopii zapasowej

**Wskazówki**: Upewnij się, że kopie zapasowe są chronione przed atakami. Powinno to obejmować szyfrowanie kopii zapasowych w celu ochrony przed utratą poufności.

W przypadku lokalnego tworzenia kopii zapasowej przy użyciu Azure Backup szyfrowanie jest dostarczane przy użyciu podanego hasła. W przypadku zwykłej kopii zapasowej usługi Azure dane kopii zapasowej są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez platformę Azure. Możesz zaszyfrować kopię zapasową przy użyciu kluczy zarządzanych przez klienta. W takim przypadku upewnij się, że ten klucz zarządzany przez klienta w magazynie kluczy należy również do zakresu kopii zapasowych.

Pamięć podręczna Azure HPC jest również chroniona przez szyfrowanie hosta maszyny wirtualnej na dyskach zarządzanych, które przechowują dane w pamięci podręcznej, nawet jeśli zostanie dodany klucz klienta dla dysków pamięci podręcznej. Dodanie klucza zarządzanego przez klienta do szyfrowania podwójnego zapewnia klientom dodatkowy poziom zabezpieczeń. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem szyfrowanie po stronie serwera w usłudze Azure Disk Storage.

Użyj kontroli dostępu opartej na rolach w Azure Backup, Azure Key Vault lub innych zasobów, aby chronić kopie zapasowe i klucze zarządzane przez klienta. Dodatkowo można włączyć zaawansowane funkcje zabezpieczeń, aby wymagać uwierzytelniania wieloskładnikowego, zanim będzie można zmienić lub usunąć kopie zapasowe.

- [Szyfrowanie hosta maszyny wirtualnej](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Szyfrowanie po stronie serwera usługi Azure Disk Storage](../virtual-machines/disk-encryption.md)

- [Omówienie funkcji zabezpieczeń w programie Azure Backup](../backup/security-overview.md) 

- [Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta](../backup/encryption-at-rest-with-cmk.md)  

- [Jak utworzyć kopię zapasową kluczy Key Vault na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowo upewnij się, że można przywrócić kopie zapasowe kluczy zarządzanych przez klienta.

- [Jak przywrócić klucze Key Vault na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: zmniejszanie ryzyka utraty kluczy

**Wskazówki**: Upewnij się, że masz środki, aby zapobiec utracie kluczy i je odzyskać. Włącz trwałe usuwanie i przeczyszczanie ochrony w Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Jak włączyć funkcję usuwania nietrwałego i przeczyszczania w programie Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz temat [Azure Security Tests: ładu and strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania zasobami i ochrony danych 

**Wskazówki**: Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią ciągłego monitorowania i ochrony systemów i danych. Ustalanie priorytetów odnajdywania, oceny, ochrony i monitorowania o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Standard klasyfikacji danych zgodny z ryzykiem biznesowym

-   Organizacja zabezpieczeń wgląd w czynniki ryzyka i spis zasobów 

-   Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

-   Bezpieczeństwo zasobów w ramach cyklu życia

-   Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

-   Korzystanie z funkcji ochrony danych natywnych platformy Azure i innych firm

-   Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie tranzytu i w czasie spoczynku

-   Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zobacz następujące odwołania:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazyn danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Środowisko wdrażania chmury — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test wydajności Azure Security — zarządzanie zasobami](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Test porównawczy zabezpieczeń platformy Azure — Ochrona danych](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówki**: Ustanów strategię całego przedsiębiorstwa do segmentacji dostępu do zasobów przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych kontrolek.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie między typami formantów, w tym zabezpieczeniami sieci, modelami tożsamości i dostępu, a także z kontrolkami dostępu i dostępem do aplikacji.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównaj segmentację sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stan zabezpieczeń

**Wskazówki**: stałe mierzenie i łagodzenie ryzyka dla poszczególnych zasobów i środowiska, w którym są hostowane. Ustalanie priorytetów zasobów o wysokiej wartości i wysoce narażonych obszarów ataków, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Azure Security test — stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Wyrównaj role organizacji, obowiązki i accountabilities

**Wskazówki**: Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu zapewnienia jasnej odpowiedzialności za decyzje dotyczące zabezpieczeń, przenosząc wszystkie osoby na współużytkowany model odpowiedzialności i przeszkolej zespoły techniczne dotyczące technologii, aby zabezpieczyć chmurę.

- [Najlepsze rozwiązanie w zakresie zabezpieczeń Azure 1 — ludzie: Edukacja zespołów w podróży w zakresie zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: Edukacja zespołów w technologii zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: Przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

**Wskazówki**: ustanowienie podejścia zabezpieczeń sieci platformy Azure jako części ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

-   Model segmentacji sieci wirtualnej wyrównany do strategii segmentacji przedsiębiorstwa

-   Strategia korygowania w różnych scenariuszach zagrożeń i ataków

-   Strategia internetowa i dotycząca ruchu przychodzącego i wychodzącego

-   Strategia międzyłączności w chmurze hybrydowej i lokalnej

-   Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, Architektura sieci odniesienia)

Aby uzyskać więcej informacji, zobacz następujące odwołania:
- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Test porównawczy zabezpieczeń platformy Azure — Zabezpieczenia sieci](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dostępu Identity i Privileged Access

**Wskazówki**: ustanowienie podejścia do tożsamości platformy Azure i dostępu uprzywilejowanego w ramach ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zobacz następujące odwołania:

- [Test porównawczy zabezpieczeń Azure — Zarządzanie tożsamościami](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — uprzywilejowany dostęp](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń usługi Azure Identity Management](../security/fundamentals/identity-management-overview.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówki**: Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i rozwiązywać zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analityków z alertami wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na poziomie integracji i ręcznej. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Rola i obowiązki organizacji operacji zabezpieczeń (SecOP) 

-   Dobrze zdefiniowany proces reagowania na zdarzenia z Instytutem NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień z klientami, dostawcami i zainteresowanymi stronami

-   Używanie platform macierzystych i innych firm platformy Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, dowodowych oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Aby uzyskać więcej informacji, zobacz następujące odwołania:

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — odpowiedź na zdarzenie](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 — proces. Aktualizowanie procesów odpowiedzi na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik wdrażania platformy Azure, rejestrowania i podejmowania decyzji dotyczących raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie platformy Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie usługi Azure Security test w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)

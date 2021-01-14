---
title: Podstawa zabezpieczeń platformy Azure dla Azure Data Box
description: Podstawa zabezpieczeń platformy Azure dla Azure Data Box
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2773a76ed1166a26ebc12b97550876eaa66bad14
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197323"
---
# <a name="azure-security-baseline-for-azure-data-box"></a>Podstawa zabezpieczeń platformy Azure dla Azure Data Box

Podstawą zabezpieczeń platformy Azure dla Azure Data Box są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview.md), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: nie dotyczy; Azure Data Box nie może być skojarzona z siecią wirtualną. Możesz kontrolować ruch z urządzenie Data Box do magazynu hostowanego na platformie Azure za pośrednictwem Azure Portal. W przypadku korzystania z urządzenie Data Box dane są przesyłane za pośrednictwem sieci szkieletowej platformy Azure.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: nie dotyczy; Azure Data Box nie może być skojarzona z siecią wirtualną. Możesz kontrolować ruch z urządzenie Data Box do magazynu hostowanego na platformie Azure za pośrednictwem Azure Portal. W przypadku korzystania z urządzenie Data Box dane są przesyłane za pośrednictwem sieci szkieletowej platformy Azure.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: nie dotyczy; Azure Data Box nie może być skojarzona z siecią wirtualną. Możesz kontrolować ruch z urządzenie Data Box do magazynu hostowanego na platformie Azure za pośrednictwem Azure Portal. W przypadku korzystania z urządzenie Data Box dane są przesyłane za pośrednictwem sieci szkieletowej platformy Azure.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: nie dotyczy; Azure Data Box nie może być skojarzona z siecią wirtualną. Możesz kontrolować ruch z urządzenie Data Box do magazynu hostowanego na platformie Azure za pośrednictwem Azure Portal. W przypadku korzystania z urządzenie Data Box dane są przesyłane za pośrednictwem sieci szkieletowej platformy Azure.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Wskazówki: punkty końcowe używane przez Azure Data Box są zarządzane przez firmę Microsoft. Użytkownik jest odpowiedzialny za wszelkie dodatkowe kontrolki, które mają zostać wdrożone w systemach lokalnych.

* [Informacje o zabezpieczeniach Azure Data Box](./data-box-security.md)

* [Informacje o porcie dla Azure Data Box](./data-box-system-requirements.md#port-requirements)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: nie dotyczy; Azure Data Box nie może być skojarzona z siecią wirtualną.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: nie dotyczy; Azure Data Box nie może być skojarzona z siecią wirtualną.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: nie dotyczy; Azure Data Box nie może być skojarzona z siecią wirtualną.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: nie dotyczy; Azure Data Box nie może być skojarzona z siecią wirtualną.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft utrzymuje źródło czasu używane dla zasobów platformy Azure, na przykład sygnatury czasowe w dziennikach. Czas Azure Data Box może nadryfować, jeśli nie jest podłączony do sieci, która może uzyskać dostęp do serwera NTP w lokacji klienta.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: odpowiadające każdemu krokowi w kolejności urządzenie Data Box można wykonać wiele akcji, aby kontrolować dostęp do zamówienia, przeprowadzać inspekcję zdarzeń, śledzić kolejność i interpretować różne dzienniki, które są generowane.

* [Zrozumienie śledzenia i rejestrowania zdarzeń dla Azure Data Box](./data-box-logs.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: odpowiadające każdemu krokowi w kolejności urządzenie Data Box można wykonać wiele akcji, aby kontrolować dostęp do zamówienia, przeprowadzać inspekcję zdarzeń, śledzić kolejność i interpretować różne dzienniki, które są generowane.

* [Zrozumienie śledzenia i rejestrowania zdarzeń dla Azure Data Box](./data-box-logs.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: to zalecenie jest przeznaczone do zasobów obliczeniowych. Urządzenie Data Box zawiera dzienniki inspekcji i pakiet pomocy technicznej zawierające dzienniki zabezpieczeń.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: nie dotyczy

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: odpowiadające każdemu krokowi w kolejności urządzenie Data Box można wykonać wiele akcji, aby kontrolować dostęp do zamówienia, przeprowadzać inspekcję zdarzeń, śledzić kolejność i interpretować różne dzienniki, które są generowane.

* [Zrozumienie śledzenia i rejestrowania zdarzeń dla Azure Data Box](./data-box-logs.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: odpowiadające każdemu krokowi w kolejności urządzenie Data Box można wykonać wiele akcji, aby kontrolować dostęp do zamówienia, przeprowadzać inspekcję zdarzeń, śledzić kolejność i interpretować różne dzienniki, które są generowane.

* [Zrozumienie śledzenia i rejestrowania zdarzeń dla Azure Data Box](./data-box-logs.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; Azure Data Box nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: nie dotyczy; Azure Data Box nie przetwarza ani nie tworzy dzienników związanych z usługą DNS.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: przechowywanie spisu kont użytkowników, które mają dostęp administracyjny do Azure Data Box. Za pomocą okienka tożsamość i kontrola dostępu (IAM) w Azure Portal subskrypcji możesz skonfigurować kontrolę dostępu opartą na rolach (Azure RBAC). Role są stosowane do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych w Active Directory. można kontrolować, kto może uzyskiwać dostęp do zamówienia podczas pierwszego tworzenia zamówienia. Skonfiguruj role platformy Azure w różnych zakresach, aby kontrolować dostęp do kolejności urządzenie Data Box. Rola platformy Azure określa typ dostępu — do odczytu i zapisu, tylko do odczytu, do odczytu i zapisu do podzbioru operacji.

* [Informacje o rolach niestandardowych](../role-based-access-control/custom-roles.md)

* [Jak skonfigurować usługę Azure RBAC dla skoroszytów](../sentinel/quickstart-get-visibility.md)

* [Informacje na temat konfigurowania kontroli dostępu w kolejności](./data-box-logs.md#set-up-access-control-on-the-order)

**Monitorowanie Azure Security Center**: nie

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: usługa Azure AD nie ma koncepcji domyślnych haseł. Inne zasoby platformy Azure wymagające hasła wymuszają utworzenie hasła przy użyciu wymagań dotyczących złożoności i minimalnej długości hasła, które różnią się w zależności od usługi. Użytkownik jest odpowiedzialny za aplikacje innych firm i usługi Marketplace, które mogą korzystać z domyślnych haseł.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

Ponadto, aby ułatwić śledzenie dedykowanych kont administracyjnych, można użyć zaleceń z Azure Security Center lub wbudowanych zasad platformy Azure, takich jak:
- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

* [Jak używać Azure Security Center do monitorowania tożsamości i dostępu (wersja zapoznawcza)](../security-center/security-center-identity-access.md)

* [Jak używać Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: nie dotyczy; dostęp do urządzenie Data Box zamówienia odbywa się za pomocą Azure Portal i zarezerwowanych dla kont z rolą dzierżawy właściciela lub współautora.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: nie dotyczy

**Monitorowanie Azure Security Center**: nie

**Odpowiedzialność**: nie dotyczy

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj stacji roboczej dostępu uprzywilejowanego (dostępem uprzywilejowanym) z włączoną usługą Azure AD Multi-Factor Authentication (MFA), aby zalogować się do i skonfigurować zamówienia Azure Data Box.

* [Stacje robocze z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication opartej na chmurze](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: nie dotyczy, Azure Data Box nie ma własnego konta administracyjnego. Dostęp do niego odbywa się za pomocą Azure Portal. Można jednak skonfigurować subskrypcję platformy Azure, aby używać usługi Azure Active Directory (AD) Privileged Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Ponadto za pomocą funkcji wykrywania ryzyka usługi Azure AD można wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

* [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Omówienie wykrywania ryzyka usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp do Azure Portal tylko z określonych logicznych grup zakresów lub krajów/regionów adresów IP.

* [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji, jeśli ma to zastosowanie. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

* [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory (AD) zawiera dzienniki ułatwiające odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

W przypadku urządzenia urządzenie Data Box nie jest to obsługiwane w czasie rzeczywistym. Dzienniki można przejrzeć na końcu zadania.

* [Informacje o raportowaniu usługi Azure AD](../active-directory/reports-monitoring/index.yml)

* [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Użyj Azure Active Directory (AD) jako centralnego systemu uwierzytelniania i autoryzacji, jeśli ma to zastosowanie. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Masz dostęp do źródeł działań związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem dzienników zdarzeń, które umożliwiają integrację z platformą Azure, lub SIEM innych firm.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty dzienników można skonfigurować w ramach Log Analytics.

Dzienniki usługi Azure Data Box nie są zapisywane do Log Analytics obszaru roboczego.

* [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Jak przejść do tablicy wskaźnikowej platformy Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: w celu odróżnienia działania logowania do konta na płaszczyźnie kontroli (np. Azure Portal) użyj funkcji Azure AD Identity Protection i wykrywania ryzyka, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

* [Jak wyświetlić ryzykowne logowanie do usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: skrytka klienta nie są obecnie obsługiwane dla Azure Data Box.

* [Lista usług obsługiwanych przez Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: nie dotyczy Azure Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Azure Data Box zostanie zainicjowana w ramach subskrypcji, w której znajdują się zasoby, do których uzyskuje się dostęp. Brak publicznego punktu końcowego do ochrony lub izolowania. Dostęp urządzenie Data Box jest dostępny dla użytkowników z dostępem właściciela lub współautora do subskrypcji.

Podczas przekazywania danych na platformę Azure, urządzenie urządzenie Data Box i usługa służąca do przekazywania danych są odizolowane.

* [Jak rozpocząć pracę z usługą Azure Data Box](./data-box-quickstart-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: Firma Microsoft zarządza podstawową infrastrukturą dla Azure Data Box i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów. Gdy urządzenie Data Box jest w witrynie klienta, należy postępować zgodnie z najlepszymi rozwiązaniami w celu zapewnienia ochrony danych poufnych.

* [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Firma Microsoft zarządza podstawową infrastrukturą dla Azure Data Box i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów. Gdy urządzenie Data Box jest w witrynie klienta, należy postępować zgodnie z najlepszymi rozwiązaniami w celu zapewnienia ochrony danych poufnych.

* [Omówienie migracji danych w Azure Data Box](./data-box-faq.md)

* [Omówienie zabezpieczeń urządzenie Data Box](./data-box-security.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: obecnie niedostępne; funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Data Box. Firma Microsoft zarządza podstawową infrastrukturą dla Azure Data Box i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

* [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: nie dotyczy

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Upewnij się, że masz właściciela lub dostęp współautora do subskrypcji, aby utworzyć zamówienie urządzenie Data Box. Można również zdefiniować urządzenie Data Box czytelnik i urządzenie Data Box role współautor na poziomie zasobu.

* [Informacje na temat rozpoczynania pracy z usługą Azure Data Box](./data-box-quickstart-portal.md)

* [Informacje o konfigurowaniu kontroli dostępu](./data-box-logs.md#set-up-access-control-on-the-order)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych. Firma Microsoft zarządza podstawową infrastrukturą dla Azure Data Box i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

* [Ochrona danych klienta platformy Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: Azure Data Box IMPLEMENTUJE szyfrowanie AES 256-bitowe dla danych w spoczynku.

Azure Data Box implementuje szyfrowanie AES 256-bitowe dla danych — w czasie spoczynku. Ponadto Azure Data Box chroni klucz odblokowywania urządzenia (znany również jako hasło urządzenia), które jest używane do blokowania urządzenia za pomocą klucza szyfrowania. Domyślnie klucz odblokowania urządzenia dla zamówienia urządzenie Data Box jest szyfrowany przy użyciu klucza zarządzanego przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczem odblokowywania urządzenia, możesz również podać klucz zarządzany przez klienta. Klucze zarządzane przez klienta muszą być tworzone i przechowywane w Azure Key Vault.

* [Zrozumienie urządzenie Data Box ochrony danych](./data-box-security.md)

* [Użyj kluczy zarządzanych przez klienta w Azure Key Vault Azure Data Box](./data-box-customer-managed-encryption-key-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla momentu wprowadzenia zmian w Azure Data Box, jak również inne krytyczne lub pokrewne zasoby.

* [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Azure Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: po wysłaniu urządzenie Data Box jest on instalowany z najnowszymi aktualizacjami. Nie wykonujemy aktualizacji pól.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: po wysłaniu urządzenie Data Box jest on instalowany z najnowszymi aktualizacjami. Nie wykonujemy aktualizacji pól.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Azure Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Azure Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: nie dotyczy, nie ma urządzenie Data Box zasobów do odnalezienia.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: nie dotyczy, nie ma metadanych zasobów dla urządzenie Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: nie dotyczy, usługa urządzenie Data Box zapewnia, że nie są używane żadne nieautoryzowane zasoby platformy Azure.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: nie dotyczy; Brak na poziomie usługi urządzenie Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: nie dotyczy, na poziomie usługi urządzenie Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy, na poziomie usługi urządzenie Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy, na poziomie usługi urządzenie Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy, na poziomie usługi urządzenie Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: nie dotyczy, usługi urządzenie Data Box korzystają tylko z zatwierdzonych usług platformy Azure.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: nie dotyczy; Usługi urządzenie Data Box korzystają tylko z zatwierdzonych tytułów oprogramowania.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

* [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; Usługa urządzenie Data Box nie obsługuje wykonywania skryptów.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; Usługa urządzenie Data Box nie ma aplikacji sieci Web uruchomionych w usłudze Azure App Service.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Azure Data Box zawiera wstępnie skonfigurowane ustawienia zabezpieczeń najlepszych rozwiązań.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: Azure Data Box zawiera wstępnie skonfigurowane ustawienia zabezpieczeń najlepszych rozwiązań.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Azure Data Box zawiera wstępnie skonfigurowane ustawienia zabezpieczeń najlepszych rozwiązań dla zasobów i nie można ich zmienić.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: Azure Data Box zawiera wstępnie skonfigurowane ustawienia zabezpieczeń najlepszych rozwiązań dla zasobów i nie można ich zmienić.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: wszystkie konfiguracje urządzenie Data Box są bezpiecznie przechowywane.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: wszystkie urządzenie Data Box obrazy systemu operacyjnego są bezpiecznie przechowywane.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: nie dotyczy, nie można zmienić konfiguracji Azure Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: nie dotyczy, nie można zmienić konfiguracji Azure Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: nie dotyczy, nie można zmienić konfiguracji Azure Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy, nie można zmienić konfiguracji Azure Data Box.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: klucze zarządzane przez klienta muszą być tworzone i przechowywane w Azure Key Vault.

* [Jak używać kluczy zarządzanych przez klienta w Azure Key Vault dla Azure Data Box](./data-box-customer-managed-encryption-key-portal.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: nie dotyczy; Azure Data Box nie korzysta z zarządzanych tożsamości.

* [Usługi platformy Azure, które obsługują tożsamości zarządzane](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Firma Microsoft uruchamia skaner poświadczeń na urządzenie Data Box kodzie. Ponadto firma Microsoft bezpiecznie chroni poświadczenia. Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

* [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: brak

**Odpowiedzialność**: Współużytkowane

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście, który obsługuje usługi platformy Azure (na przykład Azure App Service), ale nie jest uruchamiane w treści klienta.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure skrytka klienta), ale nie jest ono uruchamiane w treści klienta.

Ponosisz odpowiedzialność za wstępne skanowanie zawartości przekazywanej do zasobów platformy Azure, które nie są obliczeniowe. Firma Microsoft nie może uzyskać dostępu do danych klienta i w związku z tym nie może przeprowadzić skanowania zawartości klienta przez oprogramowanie chroniące przed złośliwym kodem.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure, ale nie jest uruchamiane w treści klienta.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: nie dotyczy, usługa urządzenie Data Box nie wymaga wykonywania kopii zapasowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: nie dotyczy

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: należy utworzyć kopię zapasową danych i klucza zarządzanego przez klienta. Urządzenie Data Box nie wykonuje żadnych kopii zapasowych.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: należy upewnić się, że wszystkie dane są na koncie usługi Azure Storage przed usunięciem go z lokalizacji lokalnej.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Upewnij się, że wszystkie kopie zapasowe lub klucze zarządzane przez klienta są chronione zgodnie z najlepszymi rozwiązaniami.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

* [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Skorzystaj z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne. Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

* [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

* [Organizowanie zasobów platformy Azure przy użyciu tagów](../azure-resource-manager/management/tag-resources.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

* [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

* [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

* [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

* [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi poprzez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

* [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Firma Microsoft przeprowadza testowanie penetracji i skanowanie luk w zabezpieczeniach na urządzenie Data Box urządzeniach. Możesz wykonywać własne testy penetracji i skanować luki w zabezpieczeniach. Jeśli zdecydujesz się to zrobić, postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj z strategii firmy Microsoft i wykonywania testów na żywo z obsługą tworzenia zespołu, usług i aplikacji w chmurze, które są zarządzane przez firmę Microsoft.

* [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../security/benchmarks/security-baselines-overview.md)
---
title: Punkt odniesienia zabezpieczeń platformy Azure dla usługi Azure Storage
description: Punkt odniesienia zabezpieczeń usługi Azure Storage zawiera wskazówki proceduralne i zasoby dotyczące wdrażania zaleceń dotyczących zabezpieczeń określonych w te testach porównawczych zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 28d5bdc788e3b292545fd4c016fae36149f3a600
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589452"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Punkt odniesienia zabezpieczeń platformy Azure dla usługi Azure Storage

Ten punkt odniesienia zabezpieczeń stosuje wskazówki z testu porównawczego zabezpieczeń platformy [Azure w wersji 1.0 do](../../security/benchmarks/overview-v1.md) usługi Azure Storage. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontrolek zabezpieczeń zdefiniowanych** przez test porównawczy zabezpieczeń platformy Azure i powiązanych wskazówek dotyczących usługi Azure Storage. **Wykluczono** kontrolki, które nie mają zastosowania do usługi Azure Storage.

 
Aby zobaczyć, jak usługa Azure Storage całkowicie mapuje się na test porównawczy zabezpieczeń platformy Azure, zobacz pełny plik mapowania punktów odniesienia [zabezpieczeń usługi Azure Storage.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1. Ochrona zasobów platformy Azure w sieciach wirtualnych

**Wskazówki:** Skonfiguruj zaporę konta magazynu, ograniczając dostęp do klientów z określonych zakresów publicznych adresów IP, wybierz sieci wirtualne lub określone zasoby platformy Azure.  Możesz również skonfigurować prywatne punkty końcowe, aby ruch do usługi magazynu z przedsiębiorstwa był przesyłany wyłącznie przez sieci prywatne.

Uwaga: klasyczne konta magazynu nie obsługują zapór i sieci wirtualnych.

- [Jak skonfigurować zaporę usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Jak skonfigurować prywatne punkty końcowe dla usługi Azure Storage](storage-private-endpoints.md)

**Odpowiedzialność**: Klient

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Storage:**

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorowanie i rejestrowanie konfiguracji i ruchu sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki:** Usługa Azure Storage udostępnia warstwowy model zabezpieczeń. Dostęp do konta magazynu można ograniczyć do żądań pochodzących z określonych adresów IP, zakresów adresów IP lub z listy podsieci w usłudze Azure Virtual Network. Możesz użyć Azure Security Center i postępować zgodnie z zaleceniami ochrony sieci, aby ułatwić zabezpieczanie zasobów sieciowych na platformie Azure. Ponadto włącz dzienniki przepływu sieciowej grupy zabezpieczeń dla sieci wirtualnych lub podsieci skonfigurowanych dla kont magazynu za pośrednictwem zapory konta magazynu i wyślij dzienniki do konta magazynu w celu inspekcji ruchu. 

 
Należy pamiętać, że jeśli do konta magazynu są dołączone prywatne punkty końcowe, nie można skonfigurować reguł sieciowej grupy zabezpieczeń dla podsieci. 
 

 
- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](storage-network-security.md) 
 

 
- [Jak włączyć dzienniki przepływu sieciowej organizacji zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [Opis zabezpieczeń sieci zapewnianych przez Azure Security Center](../../security-center/security-center-network-recommendations.md) 
 

 
- [Opis prywatnych punktów końcowych dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrona krytycznych aplikacji internetowych

**Wskazówki:** Nie dotyczy; Zalecenie jest przeznaczone dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki:** włączanie Azure Defender magazynu dla konta usługi Azure Storage. Usługa Azure Defender for Storage zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub ich wykorzystania.  Azure Security Center są oparte na działaniach, dla których komunikacja sieciowa została skojarzona z pomyślnie rozwiązanym adresem IP, bez względu na to, czy adres IP jest znanym ryzykownym adresem IP (na przykład znanym kryptografiarzem) czy adresem IP, który nie został wcześniej rozpoznany jako ryzykowny. Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie w aktywności. 

- [Konfigurowanie usługi Azure Defender dla usługi Storage](azure-defender-storage-configure.md) 

- [Understand Azure Security Center Integrated Threat Intelligence](../../security-center/azure-defender.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="15-record-network-packets"></a>1.5. Rejestrowanie pakietów sieciowych

**Wskazówki:** Network Watcher przechwytywanie pakietów umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu między kontem magazynu a maszyną wirtualną. Dla sesji przechwytywania są udostępniane filtry, aby zapewnić przechwytywanie tylko ruchu, który ma zostać przechwycony. Przechwytywanie pakietów ułatwia diagnozowanie anomalii sieciowych, zarówno reaktywnych, jak i proaktywnych. Inne zastosowania obejmują zbieranie statystyk sieci, uzyskiwanie informacji na temat włamań do sieci, debugowanie komunikacji klient-serwer i wiele innych. Możliwość zdalnego wyzwalania przechwytywania pakietów ułatwia ręczne uruchamianie przechwytywania pakietów na żądanej maszynie wirtualnej, co pozwala zaoszczędzić cenny czas. 

- [ Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher przy użyciu portalu](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6. Wdrażanie sieciowych systemów wykrywania/zapobiegania włamań (IDS/IPS)

**Wskazówki:** Azure Defender magazynu zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub wykorzystania ich. Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie w aktywności. Te alerty zabezpieczeń są zintegrowane z usługą Azure Security Center, a także są wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń. 

- [Konfigurowanie usługi Azure Defender dla usługi Storage](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Zarządzanie ruchem do aplikacji internetowych

**Wskazówki:** Nie dotyczy; Zalecenie jest przeznaczone dla aplikacji internetowych działających w Azure App Service zasobów obliczeniowych.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8. Zminimalizuj złożoność i koszty administracyjne związane z regułami zabezpieczeń sieci

**Wskazówki:** W przypadku zasobów w sieciach wirtualnych, które wymagają dostępu do konta magazynu, użyj tagów usługi Virtual Network Service dla skonfigurowanego serwera Virtual Network, aby zdefiniować kontrolę dostępu do sieci w sieciowych grupach zabezpieczeń lub Azure Firewall. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (na przykład Storage) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów obejmującymi tag usługi i automatycznie aktualizuje tag usługi w przypadku zmiany adresów. 

Gdy dostęp sieciowy musi być ograniczony do określonych kont magazynu, należy użyć Virtual Network punktu końcowego usługi.

- [Aby uzyskać więcej informacji na temat korzystania z tagów usługi](../../virtual-network/service-tags-overview.md)

- [Aby uzyskać więcej informacji na temat zasad punktu końcowego usługi dla sieci wirtualnej dla usługi Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki:** Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z kontem usługi Azure Storage przy użyciu Azure Policy. Użyj Azure Policy aliasów w przestrzeniach nazw "Microsoft.Storage" i "Microsoft.Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci zasobów konta magazynu. 

Możesz również korzystać z wbudowanych definicji zasad związanych z kontem magazynu, takich jak: Konta magazynu powinny używać punktu końcowego usługi dla sieci wirtualnej 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy przykłady dla usługi Storage](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [Azure Policy przykłady dla sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Jak utworzyć usługę Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentuj reguły konfiguracji ruchu

**Wskazówki:** użyj tagów dla sieciowych grup zabezpieczeń i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu. Tagowanie umożliwia skojarzenie wbudowanych i niestandardowych par nazwa-wartość z danym zasobem sieciowym, co ułatwia organizowanie zasobów sieciowych i powiązanie zasobów platformy Azure z projektem sieci.

Użyj dowolnej wbudowanej definicji usługi Azure Policy związanej z tagami, takiej jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone z tagami, i powiadamiać o istniejących nieotagowanych zasobach. 

Sieciowe grupy zabezpieczeń obsługują tagi, ale poszczególne reguły zabezpieczeń tego nie robią. Reguły zabezpieczeń mają pole **Opis,** które umożliwia przechowywanie niektórych informacji, które zwykle umieszcza się w tagu.

- [Jak tworzyć tagi zasobów i ich używać](../../azure-resource-manager/management/tag-resources.md)

- [Jak filtrować ruch sieciowy przy użyciu sieciowej grupy zabezpieczeń](../../virtual-network/tutorial-filter-network-traffic.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11. Monitorowanie konfiguracji zasobów sieciowych i wykrywanie zmian za pomocą zautomatyzowanych narzędzi

**Wskazówki:** użyj Azure Policy, aby rejestrować zmiany konfiguracji zasobów sieciowych.  Utwórz alerty w Azure Monitor, które będą wyzwalane w przypadku zmiany krytycznych zasobów sieciowych.  

 
- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Jak tworzyć alerty w Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: rejestrowanie i monitorowanie.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2. Konfigurowanie centralnego zarządzania dziennikami zabezpieczeń

**Wskazówki:** pozyskaj dzienniki za pośrednictwem usługi Azure Monitor, aby agregować dane zabezpieczeń generowane przez urządzenia punktów końcowych, zasoby sieciowe i inne systemy zabezpieczeń. W ramach Azure Monitor użyj obszarów roboczych usługi Log Analytics, aby wykonywać zapytania i przeprowadzać analizy, a następnie użyj kont usługi Azure Storage do przechowywania długoterminowego/archiwalnej, opcjonalnie z funkcjami zabezpieczeń, takimi jak magazyn niezmienny i wymuszone przechowywanie.

 
- [Jak zbierać dzienniki i metryki platformy za pomocą Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3. Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki:** Azure Storage Analytics dzienniki dla obiektów blob, kolejek i tabel. Możesz użyć Azure Portal, aby skonfigurować dzienniki, które są rejestrowane dla Twojego konta.   

 
- [Jak skonfigurować monitorowanie dla konta usługi Azure Storage](manage-storage-analytics-logs.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="25-configure-security-log-storage-retention"></a>2.5. Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki:** Podczas przechowywania dzienników zdarzeń zabezpieczeń na koncie usługi Azure Storage lub w obszarze roboczym usługi Log Analytics można ustawić zasady przechowywania zgodnie z wymaganiami organizacji.  

 
- [Jak skonfigurować zasady przechowywania dla dzienników konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [Zmiana okresu przechowywania danych w uciekierach usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="26-monitor-and-review-logs"></a>2.6. Monitorowanie i przeglądanie dzienników

**Wskazówki:** Aby przejrzeć dzienniki usługi Azure Storage, dostępne są typowe opcje, takie jak zapytania za pośrednictwem oferty Log Analytics, a także unikatowa opcja bezpośredniego wyświetlania plików dziennika. W usłudze Azure Storage dzienniki są przechowywane w obiektach blob, do których dostęp jest uzyskiwany bezpośrednio z witryny (folder rejestrowania jest domyślnie ukryty, więc musisz nawigować `http://accountname.blob.core.windows.net/$logs` bezpośrednio. Nie będzie ona wyświetlana w poleceniach listy) 

 
Ponadto włącz Azure Defender magazynu dla konta magazynu. Usługa Azure Defender for Storage zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub ich wykorzystania. Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie w aktywności. Te alerty zabezpieczeń są zintegrowane z usługą Azure Security Center, a także są wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń. 
 

 
- [Rejestrowanie i przeglądanie danych](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Konfigurowanie usługi Azure Defender dla usługi Storage](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7. Włączanie alertów dla niesłychanych działań

**Wskazówki:** w Azure Security Center włącz Azure Defender dla konta magazynu. Włącz ustawienia diagnostyczne dla konta magazynu i wysyłaj dzienniki do obszaru roboczego usługi Log Analytics. Dołączanie obszaru roboczego usługi Log Analytics do Azure Sentinel zapewnia rozwiązanie do automatycznej odpowiedzi aranżacji zabezpieczeń (SOAR). Dzięki temu podręczniki (rozwiązania automatyczne) mogą być tworzone i używane do rozwiązywania problemów z zabezpieczeniami.  

 
- [Jak do dołączać Azure Sentinel](../../sentinel/quickstart-onboard.md)  

 
- [Jak zarządzać alertami w Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Jak rejestrować alerty dotyczące danych dzienników analizy dzienników](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Rejestrowanie usługi Azure Storage Analytics](storage-analytics-logging.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="28-centralize-anti-malware-logging"></a>2.8: Scentralizowanie rejestrowania ochrony przed złośliwym oprogramowaniem

**Wskazówki:** Użyj usługi Azure Security Center i włącz usługę Azure Defender for Storage do wykrywania przekazywania złośliwego oprogramowania do usługi Azure Storage przy użyciu analizy reputacji skrótów i podejrzanego dostępu z aktywnego węzła wyjściowego Tor (anonimizacji serwera proxy). 

- [Konfigurowanie usługi Azure Defender dla usługi Storage](azure-defender-storage-configure.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="29-enable-dns-query-logging"></a>2.9: Włączanie rejestrowania zapytań DNS

**Wskazówki:** Azure DNS Analytics (wersja zapoznawcza) w systemie Azure Monitor zbiera szczegółowe informacje o infrastrukturze DNS dotyczące zabezpieczeń, wydajności i operacji.  Obecnie nie obsługuje to kont usługi Azure Storage, ale można użyć rozwiązania do rejestrowania DNS innych firm.  

 
- [Zbieranie szczegółowych informacji o infrastrukturze DNS przy użyciu rozwiązania DNS Analytics w wersji zapoznawczej](../../azure-monitor/insights/dns-analytics.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz Test porównawczy zabezpieczeń platformy [Azure: tożsamość i Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Utrzymywanie spisu kont administracyjnych

**Wskazówki:** Azure Active Directory (Azure AD) ma wbudowane role, które muszą być jawnie przypisane i które mogą być odpytywalne. Moduł Azure AD PowerShell umożliwia wykonywanie zapytań ad hoc w celu odnajdywania kont, które są członkami grup administracyjnych.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Zmień domyślne hasła tam, gdzie ma to zastosowanie

**Wskazówki:** Konta usługi Azure Storage Azure Active Directory (Azure AD) mają pojęcie domyślnych lub pustych haseł. Usługa Azure Storage implementuje model kontroli dostępu, który obsługuje kontrolę dostępu opartą na rolach (RBAC) na platformie Azure, a także klucz wspólny i sygnatury dostępu współdzielonego (SAS). Cechą klucza wspólnego i uwierzytelniania sygnatury dostępu współdzielonego jest to, że żadna tożsamość nie jest skojarzona z elementem wywołującym i dlatego nie można wykonać autoryzacji opartej na uprawnieniach podmiotu zabezpieczeń.

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](storage-auth.md)

- [Opis podmiotów zabezpieczeń i kontroli dostępu dla konta usługi Azure Storage](storage-introduction.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Użyj dedykowanych kont administracyjnych

**Wskazówki:** Tworzenie standardowych procedur operacyjnych dotyczących używania dedykowanych kont administracyjnych, które mają dostęp do konta usługi Storage. Użyj Azure Security Center zarządzania tożsamościami i dostępem do monitorowania liczby kont administracyjnych.

Dostęp Just-In-Time/Just-Enough-Access można również włączyć przy użyciu usługi Azure Active Directory (Azure AD) Privileged Identity Management Privileged Roles for Microsoft Services i Azure Resource Manager.

- [Opis Azure Security Center tożsamości i dostępu](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management omówienie](/azure/active-directory/privileged-identity-management/)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Używanie Azure Active Directory jednokrotnego (SSO)

**Wskazówki:** wszędzie tam, gdzie to możliwe, należy Azure Active Directory logowania jednokrotnego (Azure AD) zamiast konfigurować indywidualne poświadczenia autonomiczne dla usługi. Użyj Azure Security Center tożsamości i zarządzania dostępem.
 

 
- [Opis logowania jednokrotnego w usłudze Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Autoryzowanie dostępu do danych w usłudze Azure Storage](storage-auth.md)
 

 
- [Autoryzowanie dostępu do obiektów blob i kolejek przy użyciu usługi Azure AD](storage-auth-aad.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Używaj uwierzytelniania wieloskładnikowego dla Azure Active Directory dostępu opartego na danych

**Wskazówki:** włącz uwierzytelnianie wieloskładnikowe usługi Azure Active Directory (Azure AD) i postępuj zgodnie z Azure Security Center tożsamościami i zarządzaniem dostępem, aby chronić zasoby konta magazynu.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6. Używanie bezpiecznych stacji roboczych zarządzanych przez platformę Azure do wykonywania zadań administracyjnych

**Wskazówki:** Używanie stacji roboczych z dostępem uprzywilejowanym z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów konta magazynu.  

 
- [Dowiedz się więcej o stacjach roboczych z dostępem uprzywilejowanym](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Rejestrowanie i alerty dotyczące podejrzanych działań z kont administracyjnych

**Wskazówki:** wysyłanie Azure Security Center alertów dotyczących wykrywania ryzyka do Azure Monitor i konfigurowanie niestandardowych alertów/powiadomień przy użyciu grup akcji. Włącz Azure Defender konta usługi Storage, aby generować alerty dotyczące podejrzanych działań. Ponadto wykrywanie ryzyka Azure Active Directory (Azure AD) w celu wyświetlania alertów i raportów dotyczących ryzykownych zachowań użytkowników.

- [Konfigurowanie usługi Azure Defender dla usługi Storage](azure-defender-storage-configure.md)
 

- [Opis wykrywania ryzyka w usłudze Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [Jak skonfigurować grupy akcji dla niestandardowych alertów i powiadomień](../../azure-monitor/alerts/action-groups.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki:** Użyj dostępu warunkowego nazwanych lokalizacji, aby zezwolić na dostęp tylko z określonych grup logicznych zakresów adresów IP lub krajów/regionów. 

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="39-use-azure-active-directory"></a>3.9. Użyj Azure Active Directory

**Wskazówki:** Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Platforma Azure zapewnia kontrolę dostępu opartą na rolach (RBAC) na platformie Azure, aby uzyskać precyzyjne sterowanie dostępem klienta do zasobów na koncie magazynu.   Jeśli to możliwe, użyj poświadczeń usługi Azure AD jako najlepszego rozwiązania w zakresie zabezpieczeń, a nie klucza konta, którego bezpieczeństwo może być łatwiej naruszone. Jeśli projekt aplikacji wymaga sygnatur dostępu współdzielonego w celu uzyskania dostępu do usługi Blob Storage, użyj poświadczeń usługi Azure AD, aby utworzyć sygnatury dostępu współdzielonego delegowania użytkownika, jeśli jest to możliwe w celu uzyskania najwyższego bezpieczeństwa.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Uzyskiwanie dostępu do zasobów zarządzania za pomocą dostawcy zasobów usługi Azure Storage](authorization-resource-provider.md)

- [Jak skonfigurować dostęp do danych obiektów blob i kolejek platformy Azure przy użyciu kontroli dostępu na Azure Portal](storage-auth-aad-rbac-portal.md)

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](storage-auth.md)

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki:** Przejrzyj dzienniki usługi Azure Active Directory (Azure AD), aby ułatwić odnajdywanie nieaktualnych kont, które mogą obejmować konta z rolami administracyjnymi konta usługi Storage. Ponadto usługa Azure Identity Access Reviews umożliwia efektywne zarządzanie członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw, które mogą być używane do uzyskiwania dostępu do zasobów konta magazynu, oraz przypisań ról. Dostęp użytkowników powinien być regularnie przeglądany, aby upewnić się, że tylko odpowiedni użytkownicy mają dalszy dostęp. 
 

 
Możesz również użyć sygnatury dostępu współdzielonego (SAS), aby zapewnić bezpieczny delegowany dostęp do zasobów na koncie magazynu bez naruszania bezpieczeństwa danych. Między innymi możesz kontrolować, do jakich zasobów klient może uzyskać dostęp, jakie uprawnienia ma do tych zasobów oraz jak długo jest ważna sygnatura dostępu współdzielonego.
 

 
Przejrzyj również anonimowy dostęp do odczytu do kontenerów i obiektów blob. Domyślnie kontener i wszystkie jego obiekty blob mogą być dostępne tylko dla użytkownika, który otrzymał odpowiednie uprawnienia. Możesz użyć Azure Monitor alertów o dostępie anonimowym dla kont magazynu przy użyciu warunku uwierzytelniania anonimowego.
 

 
Jednym ze skutecznych sposobów zmniejszenia ryzyka nieświadomego dostępu do konta użytkownika jest ograniczenie czasu trwania dostępu udzielanego użytkownikom. Ograniczone czasowo identyfikatory URI sygnatury dostępu współdzielonego to jeden ze skutecznych sposobów automatycznego wygasania dostępu użytkowników do konta magazynu. Ponadto częsta rotacja kluczy konta magazynu jest sposobem zapewnienia, że nieoczekiwany dostęp za pośrednictwem kluczy konta magazynu będzie ograniczony.
 

 
- [Informacje o raportowaniu w usłudze Azure AD](/azure/active-directory/reports-monitoring/) 
 

 
- [Jak wyświetlać i zmieniać dostęp na poziomie konta usługi Azure Storage](storage-auth-aad-rbac-portal.md)
 

 
- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
 

 
- [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/anonymous-read-access-configure.md)
 

 
- [Monitorowanie konta magazynu w witrynie Azure Portal](manage-storage-analytics-logs.md)
 

 
- [Zarządzanie kluczami dostępu do konta magazynu](storage-account-keys-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorowanie prób uzyskania dostępu do dezaktywowanych poświadczeń

**Wskazówki:** użyj analityka magazynu do dzienników szczegółowych informacji o żądaniach do usługi magazynu, które zakończyły się powodzeniem i niepowodzeniem. Wszystkie dzienniki są przechowywane w blokowych obiektach blob w kontenerze o nazwie $logs, które są tworzone automatycznie, analityka magazynu są włączone dla konta magazynu.
 

Utwórz ustawienia diagnostyczne dla Azure Active Directory kont użytkowników usługi (Azure AD), wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego usługi Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym usługi Log Analytics.

Aby monitorować błędy uwierzytelniania dla kont usługi Azure Storage, możesz utworzyć alerty powiadamiające o osiągnięciu określonych progów dla metryk zasobów magazynu. Ponadto należy użyć Azure Monitor alertu o dostępie anonimowym dla kont magazynu przy użyciu warunku uwierzytelniania anonimowego.

- [Rejestrowanie usługi Azure Storage Analytics](storage-analytics-logging.md)
 

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Jak skonfigurować alerty metryk dla kont usługi Azure Storage](manage-storage-analytics-logs.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Odchylenie zachowania alertu podczas logowania na koncie

**Wskazówki:** użyj Azure Active Directory usługi Azure AD (Risk and Identity Protection) w celu skonfigurowania automatycznych odpowiedzi na wykryte podejrzane akcje związane z zasobami konta magazynu. Należy włączyć automatyczne odpowiedzi za pośrednictwem Azure Sentinel, aby zaimplementować odpowiedzi dotyczące zabezpieczeń w organizacji.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Jak skonfigurować i włączyć zasady ryzyka związane z usługą Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak do dołączać Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Zapewnienie firmie Microsoft dostępu do odpowiednich danych klientów podczas scenariuszy pomocy technicznej

**Wskazówki:** W scenariuszach pomocy technicznej, w których firma Microsoft musi uzyskać dostęp do danych klienta, usługa Skrytka klienta (wersja zapoznawcza dla konta magazynu) udostępnia klientom interfejs do przeglądania i zatwierdzania lub odrzucania żądań dostępu do danych klientów. Firma Microsoft nie będzie wymagać ani nie żądać dostępu do wpisów tajnych organizacji przechowywanych na koncie usługi Storage.

- [Opis Skrytka klienta](../../security/fundamentals/customer-lockbox-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1. Utrzymywanie spisu informacji poufnych

**Wskazówki:** używanie tagów do śledzenia zasobów konta magazynu, które przechowują lub przetwarzają informacje poufne. 

- [Jak tworzyć tagi i ich używać](../../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolowanie systemów przechowujące lub przetwarzające informacje poufne

**Wskazówki:** Implementowanie izolacji przy użyciu oddzielnych subskrypcji, grup zarządzania i kont magazynu dla poszczególnych domen zabezpieczeń, takich jak środowisko i czułość danych.  Możesz ograniczyć konto magazynu do kontrolowania poziomu dostępu do kont magazynu, których wymagają aplikacje i środowiska przedsiębiorstwa, na podstawie typu i podzestawu używanych sieci. Po skonfigurowaniu reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskać dostęp do konta magazynu. Dostęp do usługi Azure Storage można kontrolować za pośrednictwem kontroli dostępu na RBAC platformy Azure (Azure RBAC).

Możesz również skonfigurować prywatne punkty końcowe, aby zwiększyć bezpieczeństwo w przypadku ruchu między siecią wirtualną i usługą przez sieć szkieletową firmy Microsoft, eliminując narażenie z publicznego Internetu.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](storage-network-security.md)

- [Punkty końcowe usługi dla sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego transferu poufnych informacji

**Wskazówki:** W przypadku zasobów konta magazynu przechowujące lub przetwarzające poufne informacje oznacz zasoby jako poufne przy użyciu tagów. Aby zmniejszyć ryzyko utraty danych przez eksfiltrację, ogranicz wychodzący ruch sieciowy dla kont usługi Azure Storage przy użyciu Azure Firewall.  

Ponadto użyj zasad punktu końcowego usługi dla sieci wirtualnej, aby filtrować ruch sieci wirtualnej ruchu wychodzącego do kont usługi Azure Storage za pośrednictwem punktu końcowego usługi i zezwalać na eksfiltrację danych tylko dla określonych kont usługi Azure Storage.

- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Zasady punktu końcowego usługi dla sieci wirtualnej dla usługi Azure Storage](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Informacje na temat ochrony danych klientów na platformie Azure](../../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4. Szyfrowanie wszystkich informacji poufnych podczas przesyłania

**Wskazówki:** Korzystanie z protokołu HTTPS można wymusić, włączając bezpieczny transfer wymagany dla konta magazynu. Gdy ta opcja jest włączona, połączenia korzystające z protokołu HTTP będą odrzucane.  Ponadto należy użyć Azure Security Center i Azure Policy, aby wymusić bezpieczny transfer dla konta magazynu.

- [Jak wymagać bezpiecznego transferu w usłudze Azure Storage](storage-require-secure-transfer.md)

- [Zasady zabezpieczeń platformy Azure monitorowane przez Security Center](../../security-center/policy-reference.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center:** Test porównawczy zabezpieczeń platformy [Azure](/azure/governance/policy/samples/azure-security-benchmark) to domyślna inicjatywa zasad dla usługi Security Center i stanowi podstawę rekomendacji Security Center [firmy .](/azure/security-center/security-center-recommendations) Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.Storage:**

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5. Identyfikowanie poufnych danych za pomocą aktywnego narzędzia odnajdywania

**Wskazówki:** Funkcje identyfikacji danych nie są jeszcze dostępne dla konta usługi Azure Storage i powiązanych zasobów. Implementowanie rozwiązania innej firmy, jeśli jest to wymagane do celów zgodności. 

- [Informacje na temat ochrony danych klientów na platformie Azure](../../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6. Kontrola dostępu oparta na rolach umożliwia kontrolowanie dostępu do zasobów

**Wskazówki:** Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pomocą kontroli dostępu na podstawie ról (RBAC) platformy Azure. Usługa Azure Storage definiuje zestaw wbudowanych ról RBAC platformy Azure, które obejmują typowe zestawy uprawnień używanych do uzyskiwania dostępu do danych obiektów blob lub kolejek.

- [Jak przypisać role RBAC platformy Azure dla konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Uzyskiwanie dostępu do zasobów zarządzania za pomocą dostawcy zasobów usługi Azure Storage](authorization-resource-provider.md)

- [Jak skonfigurować dostęp do danych obiektów blob i kolejek platformy Azure przy użyciu kontroli dostępu na Azure Portal](storage-auth-aad-rbac-portal.md)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](storage-auth.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8. Szyfrowanie poufnych informacji w spoczynku

**Wskazówki:** Szyfrowanie usługi Azure Storage jest włączone dla wszystkich kont magazynu i nie można go wyłączyć. Usługa Azure Storage automatycznie szyfruje dane, gdy są utrwalane w chmurze. Dane odczytywane z usługi Azure Storage są odszyfrowywane przed zwróceniem. Szyfrowanie usługi Azure Storage umożliwia zabezpieczanie danych magazynowych bez konieczności modyfikowania kodu ani dodawania kodu do żadnych aplikacji. 

- [Informacje o szyfrowaniu magazynu w spoczynku w usłudze Azure Storage](storage-service-encryption.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9. Rejestrowanie zmian krytycznych zasobów platformy Azure i alerty dotyczące zmian

**Wskazówki:** użyj Azure Monitor z dziennika aktywności platformy Azure, aby tworzyć alerty dotyczące zmian w zasobach konta magazynu.  Możesz również włączyć rejestrowanie usługi Azure Storage, aby śledzić sposób autoryzacji poszczególnych żądań względem usługi Azure Storage. Dzienniki wskazują, czy żądanie zostało wykonane anonimowo, przy użyciu tokenu OAuth 2.0, klucza wspólnego lub sygnatury dostępu współdzielonego (SAS).  Ponadto należy użyć Azure Monitor alertów o dostępie anonimowym dla kont magazynu przy użyciu warunku uwierzytelniania anonimowego. 

 
- [Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Rejestrowanie usługi Azure Storage Analytics](storage-analytics-logging.md) 

 
- [Jak skonfigurować alerty metryk dla kont usługi Azure Storage](manage-storage-analytics-logs.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: zarządzanie lukami w zabezpieczeniach.](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1. Uruchamianie zautomatyzowanych narzędzi do skanowania luk w zabezpieczeniach

**Wskazówki:** postępuj zgodnie z zaleceniami Azure Security Center, aby stale monitorować i monitorować konfigurację kont magazynu.  

- [Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny](../../security-center/recommendations-reference.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porównanie skanów luk w zabezpieczeniach typu wstecz

**Wskazówki:** Nie dotyczy; Firma Microsoft wykonuje zarządzanie lukami w zabezpieczeniach w systemach bazowych, które obsługują konta magazynu.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5. Określanie priorytetów korygowania wykrytych luk w zabezpieczeniach za pomocą procesu oceny ryzyka

**Wskazówki:** Użyj domyślnych klasyfikacji ryzyka (Secure Score) dostarczonych przez Azure Security Center. 

- [Opis Azure Security Center bezpieczeństwa](/azure/security-center/security-center-secure-score)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: spis i zarządzanie zasobami.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1. Korzystanie z rozwiązania do automatycznego odnajdywania zasobów

**Wskazówki:** Użyj Azure Resource Graph, aby odpytywać i odnajdywać wszystkie zasoby (w tym konta magazynu) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia do odczytu w dzierżawie i możesz wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach twoich subskrypcji.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC na platformie Azure](../../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="62-maintain-asset-metadata"></a>6.2. Obsługa metadanych zasobów

**Wskazówki:** Stosowanie tagów do zasobów konta usługi Storage z metadanymi w celu logicznego zorganizowania ich w taksonomię. 

- [Jak tworzyć tagi i ich używać](../../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="63-delete-unauthorized-azure-resources"></a>6.3. Usuwanie nieautoryzowanych zasobów platformy Azure

**Wskazówki:** Używaj tagowania, grup zarządzania i oddzielnych subskrypcji, jeśli jest to odpowiednie, do organizowania i śledzenia kont magazynu i powiązanych zasobów. Regularnie uzgadniaj spis i upewnij się, że nieautoryzowane zasoby zostaną usunięte z subskrypcji w terminowy sposób. 

Ponadto należy użyć Azure Defender magazynu do wykrywania nieautoryzowanych zasobów platformy Azure. 

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../../cost-management-billing/manage/create-subscription.md) 

- [Jak utworzyć Grupy zarządzania](../../governance/management-groups/create-management-group-portal.md)

- [Jak tworzyć tagi i ich używać](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurowanie usługi Azure Defender dla usługi Storage](azure-defender-storage-configure.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4. Definiowanie i utrzymywanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki:** należy utworzyć spis zatwierdzonych zasobów platformy Azure zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorowanie niezatwierdzonych zasobów platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad: 

 
 - Niedozwolone typy zasobów 
 
 - Dozwolone typy zasobów 
 

 
Ponadto użyj tego Azure Resource Graph do wykonywania zapytań o zasoby w ramach subskrypcji i odnajdywania ich. Może to pomóc w środowiskach o wysokim poziomie zabezpieczeń, takich jak środowiska z kontami magazynu. 
 

 
- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7. Usuwanie niezatwierdzonych zasobów i aplikacji platformy Azure

**Wskazówki:** Klient może uniemożliwić tworzenie lub używanie zasobów Azure Policy zgodnie z wymaganiami zasad firmy klienta. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="69-use-only-approved-azure-services"></a>6.9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki:** użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Dodatkowe informacje są dostępne pod linkami, do których się odwołujesz.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Test [porównawczy zabezpieczeń](/azure/governance/policy/samples/azure-security-benchmark) platformy Azure jest domyślną inicjatywą zasad dla usługi Security Center i stanowi podstawę Security Center [rekomendacji firmy](/azure/security-center/security-center-recommendations). Definicje Azure Policy związane z tą kontrolką są włączane automatycznie przez Security Center. Alerty związane z tą kontrolą mogą [wymagać Azure Defender](/azure/security-center/azure-defender) planu dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft.ClassicStorage:**

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Azure Policy wbudowane definicje — Microsoft.Storage:**

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie możliwości interakcji użytkowników z Azure Resource Manager

**Wskazówki:** Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resource Manager, konfigurując opcję "Blokuj dostęp" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i zmiany w zasobach w środowisku o wysokim poziomie zabezpieczeń, takim jak te z kontami usługi Storage. 

- [Jak skonfigurować dostęp warunkowy, aby zablokować dostęp do Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: bezpieczna konfiguracja.](../../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki:** użyj Azure Policy w przestrzeni nazw **Microsoft.Storage,** aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji wystąpień konta usługi Storage. Możesz również użyć wbudowanych definicji Azure Policy dla konta usługi Azure Storage, takich jak:

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Wdrażanie Azure Defender magazynu
- Konta magazynu należy migrować do nowych Azure Resource Manager zasobów
- Należy włączyć bezpieczny transfer na konta magazynu

Użyj zaleceń z Azure Security Center jako bezpiecznej konfiguracji odniesienia dla kont usługi Storage.

- [Jak wyświetlić dostępne Azure Policy aliasy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Policy [deny] i [deploy if not exist], aby wymusić bezpieczne ustawienia w zasobach konta magazynu. 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../../governance/policy/tutorials/create-and-manage.md) 

- [Understanding Azure Policy Effects](../../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

**Wskazówki:** użyj Azure Repos, aby bezpiecznie przechowywać kod, taki jak niestandardowe zasady platformy Azure, szablony Azure Resource Manager, skrypty Desired State Configuration itp. Aby uzyskać dostęp do zasobów, które zarządzasz w programie Azure DevOps, możesz udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w usłudze Azure Active Directory (Azure AD), jeśli są zintegrowane z programem Azure DevOps, lub usługi Azure AD, jeśli są zintegrowane z programem TFS.

- [Jak przechowywać kod w Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informacje o uprawnieniach i grupach w Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki:** Korzystanie z Azure Policy do alertów, inspekcji i wymuszania konfiguracji systemu dla konta magazynu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad. 

- [ Jak konfigurować i zarządzać Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementowanie zautomatyzowanego monitorowania konfiguracji dla zasobów platformy Azure

**Wskazówki:** wykorzystaj Azure Security Center do przeprowadzania skanowania linii bazowej dla zasobów konta usługi Azure Storage. 

- [Jak korygować zalecenia w Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki:** Usługa Azure Storage automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze. Możesz użyć kluczy zarządzanych przez firmę Microsoft do szyfrowania konta magazynu lub zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli używasz kluczy dostarczonych przez klienta, możesz wykorzystać Azure Key Vault do bezpiecznego przechowywania kluczy. 

Ponadto często obracaj klucze konta magazynu, aby ograniczyć wpływ utraty lub ujawnienia kluczy konta magazynu.

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)

- [Zarządzanie kluczami dostępu do konta magazynu](storage-account-keys-manage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki:** Autoryzowanie dostępu do obiektów blob i kolejek w ramach kont usługi Azure Storage przy użyciu Azure Active Directory (Azure AD) i tożsamości zarządzanych. Usługi Azure Blob Storage i Queue Storage obsługują uwierzytelnianie usługi Azure AD przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. 

Tożsamości zarządzane dla zasobów platformy Azure mogą autoryzować dostęp do danych obiektów blob i kolejek przy użyciu poświadczeń usługi Azure AD z aplikacji uruchomionych w usłudze Azure Virtual Machines r, aplikacji funkcji, zestawów skalowania maszyn wirtualnych i innych usług. Używając tożsamości zarządzanych dla zasobów platformy Azure wraz z uwierzytelnianiem usługi Azure AD, można uniknąć przechowywania poświadczeń z aplikacjami uruchomionymi w chmurze.

- [Jak udzielić dostępu do danych obiektów blob i kolejek platformy Azure przy użyciu tożsamości zarządzanej](storage-auth-aad-rbac-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminowanie niezamierzonego narażenia poświadczeń

**Wskazówki:** implementowanie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [ Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: Malware Defense.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Pliki przed skanowaniem, które mają zostać przekazane do zasobów platformy Azure bez obliczeń

**Wskazówki:** użyj usługi Azure Defender storage do wykrywania przekazywania złośliwego oprogramowania do usługi Azure Storage przy użyciu analizy reputacji skrótów i podejrzanego dostępu z aktywnego węzła wyjściowego Tor (serwera proxy anonimizacji). 
 

 
Możesz również wstępnie przeskanować zawartość pod kątem złośliwego oprogramowania przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage i inne, aby spełnić wymagania organizacji.
 

 
- [Konfigurowanie usługi Azure Defender dla usługi Storage](azure-defender-storage-configure.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy zabezpieczeń platformy Azure: odzyskiwanie danych.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnianie regularnych automatycznych kopii zapasowej

**Wskazówki:** dane na koncie magazynu Microsoft Azure są zawsze automatycznie replikowane w celu zapewnienia trwałości i wysokiej dostępności. Usługa Azure Storage kopiuje dane tak, aby chronić je przed planowanymi i nieplanowanych zdarzeniami, w tym przejściowymi awariami sprzętu, awariami sieci lub zasilania oraz olbrzymimi klęskami żywiołowymi. Dane można replikować w tym samym centrum danych, w strefowych centrach danych w tym samym regionie lub w regionach oddzielonych geograficznie. 

Możesz również włączyć usługę Azure Automation, aby zwykłego tworzenia migawek obiektów blob.

- [Informacje o nadmiarowości i umowach Service-Level Azure Storage](storage-redundancy.md)

- [Tworzenie migawki obiektu blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure Automation omówienie](../../automation/automation-intro.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2. Wykonywanie pełnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

**Wskazówki:** w celu tworzenia kopii zapasowej danych z obsługiwanych usług konta magazynu dostępnych jest wiele metod, w tym użycie narzędzia azcopy lub narzędzi innych firm. Niezmienny magazyn usługi Azure Blob Storage umożliwia użytkownikom przechowywanie obiektów danych krytycznych dla działalności firmy w stanie WORM (zapis jeden raz, odczyt wielu). Ten stan sprawia, że danych nie można wymazać i nie można modyfikować przez określony przez użytkownika interwał.
 

Klucze zarządzane przez klienta lub dostarczone przez klienta mogą być Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. 

 
- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)  

- [Ustawianie zasad niezmienności dla usługi Blob Storage i zarządzanie nimi](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Zweryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki:** okresowe przywracanie danych Key Vault certyfikatów, kluczy, zarządzanych kont magazynu i wpisów tajnych za pomocą następujących poleceń programu PowerShell:

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Jak przywrócić Key Vault certyfikatów](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Jak przywrócić Key Vault kluczy](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Jak przywrócić Key Vault magazynu zarządzanego](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Jak przywrócić Key Vault tajne](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [AzCopy to narzędzie wiersza polecenia, które umożliwia kopiowanie obiektów blob, plików i danych tabeli do lub z konta magazynu](storage-use-azcopy-v10.md)

Uwaga: Jeśli chcesz skopiować dane do i z usługi Azure Table Storage, zainstaluj narzędzie AzCopy w wersji 7.3.

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4. Zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki:** Aby włączyć klucze zarządzane przez klienta na koncie magazynu, należy użyć Azure Key Vault do przechowywania kluczy. W magazynie kluczy należy włączyć zarówno właściwości Usuwanie nieuruchomione, jak i Nie przeczyszczaj.  Key Vault usuwania nie softowego umożliwia odzyskiwanie usuniętych magazynów i obiektów magazynu, takich jak klucze, wpisy tajne i certyfikaty.  W przypadku tworzenia kopii zapasowej danych konta usługi Storage w obiektach blob usługi Azure Storage włącz usuwanie nie softowe, aby zapisywać i odzyskiwać dane po usunięciu obiektów blob lub migawek obiektów blob.  Kopie zapasowe należy traktować jako poufne dane i stosować odpowiednie mechanizmy kontroli dostępu i ochrony danych w ramach tego punktu odniesienia.  Ponadto w celu poprawy ochrony można przechowywać krytyczne dla działalności firmy obiekty danych w stanie WORM (zapis jeden raz, wiele odczytów).

- [Jak używać usuwania Azure Key Vault firmy](../../key-vault/general/key-vault-recovery.md)

- [Soft delete for Azure Storage blobs](../blobs/soft-delete-blob-overview.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

- [Przechowywanie danych obiektów blob kluczowych dla działalności za pomocą magazynu niezmiennego](../blobs/storage-blob-immutable-storage.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1. Tworzenie przewodnika po reagowaniu na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Centrum zabezpieczeń firmy Microsoft Anatomy of an Incident (Anatomia zdarzenia)](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Przewodnik obsługi zdarzeń zabezpieczeń komputera NIST](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2. Tworzenie procedury oceniania i ustalania priorytetów zdarzeń

**Wskazówki:** Security Center przypisuje ważność do każdego alertu, aby ułatwić określanie priorytetów, które alerty powinny być badane w pierwszej kolejności. Ważność jest oparta na tym, jak pewna Security Center znajduje się w znajdowaniu lub analizie użytej do wystawienia alertu, a także na poziomie pewności, że za działaniem, które doprowadziło do tego alertu, znajduje się złośliwy zamiar. 

 
Ponadto wyraźnie oznaczaj subskrypcje (na przykład production, non-prod) przy użyciu tagów i utwórz system nazewnictwa, aby jasno identyfikować i kategoryzować zasoby platformy Azure, zwłaszcza te, które przetwarzają poufne dane.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.
 

 
- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../../security-center/security-center-alerts-overview.md)
 

 
- [Organizowanie zasobów platformy Azure przy użyciu tagów](../../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

**Wskazówki:** Przećwiczyj ćwiczenia, aby regularnie testować możliwości reagowania na zdarzenia w systemach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Publikacja NIST — Przewodnik po programach testowych, szkoleniowych i szkoleniowych dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4. Podaj szczegóły kontaktowe zdarzenia zabezpieczeń i skonfiguruj powiadomienia o alertach dla zdarzeń zabezpieczeń

**Wskazówki:** informacje kontaktowe dotyczące zdarzeń naruszenia zabezpieczeń będą używane przez firmę Microsoft do kontaktowania się z tobą, jeśli Centrum zabezpieczeń firmy Microsoft (MSRC) odkryje, że dostęp do danych użytkownika został uzyskany przez niedozwoloną lub niedozwoloną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt Azure Security Center zabezpieczeń](../../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5. Dołączanie alertów zabezpieczeń do systemu reagowania na zdarzenia

**Wskazówki:** eksportuj alerty Azure Security Center i zalecenia przy użyciu funkcji eksportu ciągłego, aby ułatwić identyfikowanie zagrożeń związanych z zasobami platformy Azure. Eksport ciągły umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych Azure Security Center, aby przesyłać strumieniowo alerty do Azure Sentinel.

- [Jak skonfigurować eksport ciągły](../../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki:** Użyj funkcji automatyzacji przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem Logic Apps alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.
    

- [ Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania:** Brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [test porównawczy zabezpieczeń platformy Azure: testy penetracyjne i ćwiczenia zespołu red team.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1. Przeprowadzanie regularnych testów penetracyjnych zasobów platformy Azure i zapewnianie korygowania wszystkich krytycznych ustaleń zabezpieczeń

**Wskazówki:** postępuj zgodnie z zasadami firmy Microsoft w zakresie zaangażowania, aby upewnić się, że testy penetracyjne nie naruszały zasad firmy Microsoft. Użyj strategii firmy Microsoft i wykonania czerwonego zespołu i testów penetracyjnych na żywo w infrastrukturze, usługach i aplikacjach w chmurze zarządzanych przez firmę Microsoft.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania:** Brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)

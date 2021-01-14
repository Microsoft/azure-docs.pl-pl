---
title: Podstawowa baza zabezpieczeń Azure dla usługi Batch
description: Linia bazowa usługi Batch Security zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń Azure.
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c27ef1af3e439b22f00f9247b5270118bbe9ca89
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197714"
---
# <a name="azure-security-baseline-for-batch"></a>Podstawowa baza zabezpieczeń Azure dla usługi Batch

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0 do usługi](../security/benchmarks/overview-v1.md) Batch. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące usługi Batch. **Kontrolki** , które nie mają zastosowania do partii, zostały wykluczone.

 
Aby dowiedzieć się, jak usługa Batch jest całkowicie mapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pełny plik mapowania zabezpieczeń usługi Batch](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: wdrażanie pul Azure Batch w obrębie sieci wirtualnej. Aby umożliwić węzłom obliczeniowym puli bezpieczne komunikowanie się z innymi maszynami wirtualnymi lub z siecią lokalną, można udostępnić pulę w podsieci sieci wirtualnej platformy Azure. Ponadto wdrożenie puli w ramach sieci wirtualnej zapewnia kontrolę nad sieciową grupą zabezpieczeń (sieciowej grupy zabezpieczeń) służącą do zabezpieczania interfejsów sieciowych poszczególnych węzłów (NIC), a także podsieci. Skonfiguruj sieciowej grupy zabezpieczeń, aby zezwalać na ruch tylko z zaufanych adresów IP/Locations przez Internet.

W razie potrzeby Wyłącz dostęp do sieci publicznej przy użyciu prywatnego linku platformy Azure, aby nawiązać połączenie z kontem Azure Batch za pośrednictwem prywatnego punktu końcowego. Usługa prywatnego połączenia platformy Azure jest zabezpieczona i akceptuje połączenia tylko z uwierzytelnionych i autoryzowanych prywatnych punktów końcowych. Dodatkowo można ograniczyć łączność i wykrywalność poprzez wyłączenie publicznie uwidocznionych punktów końcowych protokołu RDP/SSH dla węzłów obliczeniowych w puli usługi Batch.

- [Jak utworzyć pulę Azure Batch w ramach Virtual Network](batch-virtual-network.md)

- [Jak utworzyć konto Azure Batch z wyłączonym dostępem do sieci](private-connectivity.md)

- [Jak utworzyć prywatny punkt końcowy](../private-link/create-private-endpoint-portal.md)

- [Jak odmówić dostępu do ruchu RDP/SSH](pool-endpoint-configuration.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: Użyj Azure Security Center i skoryguj zalecenia dotyczące ochrony sieci dotyczące sieci wirtualnej/sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) skojarzonej z pulą zadań. Włącz dzienniki przepływu na sieciowej grupy zabezpieczeń używanym do ochrony puli usługi Batch i Wyślij dzienniki do konta magazynu platformy Azure na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego usługi Azure Log Analytics i użyć usługi Azure Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety platformy Azure Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i konfigurowania nieprawidłowo skonfigurowanych sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

- [Informacje o zabezpieczeniach sieci zapewnianych przez Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: Włącz usługę Azure DDoS (rozproszone odmowa usługi) w sieci wirtualnej chroniącą pulę Azure Batch w celu ochrony przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

- [Jak skonfigurować ochronę DDoS](/azure/virtual-network/manage-ddos-protection)

- [Opis Azure Security Center zintegrowanej analizy zagrożeń](/azure/security-center/security-center-alerts-service-layer)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Włączanie dzienników przepływów w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń) używanej do ochrony puli Azure Batch i wysyłania dzienników do konta usługi Azure Storage na potrzeby inspekcji ruchu.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Jeśli jest to wymagane do celów zgodności, wybierz sieciowe urządzenie wirtualne w portalu Azure Marketplace, które obsługuje systemy wykrywania wtargnięcia (identyfikatory) i systemy zapobiegania włamaniom (IP) z możliwościami inspekcji ładunku.

Jeśli wykrywanie i/lub zapobieganie włamaniu nie są wymagane w oparciu o inspekcję ładunku, można użyć zapory platformy Azure z funkcją analizy zagrożeń. Filtrowanie oparte na analizie zagrożeń na platformie Azure może wysyłać alerty i odrzucać ruch do i ze znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

Wdróż zaporę platformy Azure z publicznym adresem IP w tej samej sieci wirtualnej co węzły puli Azure Batch. Skonfiguruj reguły translacji adresów sieciowych (NAT) między zaufanymi lokalizacjami w Internecie i prywatnymi adresami IP węzłów poszczególnych pul. W obszarze Zapora systemu Azure w obszarze Analiza zagrożeń skonfiguruj opcję "Alert i Odmów", aby blokować alerty i blokować ruch do/z znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą ze źródła analizy zagrożeń firmy Microsoft i są uwzględniane tylko najwyższe rekordy zaufania. 

- [Jak utworzyć pulę Azure Batch w ramach Virtual Network](batch-virtual-network.md)

- [Jak wdrożyć zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Użyj tagów usługi sieci wirtualnej, aby zdefiniować kontrolę dostępu do sieci dla sieciowych grup zabezpieczeń lub zapór platformy Azure skojarzonych z pulami Azure Batch. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. ApiManagement) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

- [Zrozumienie i używanie tagów usługi](../virtual-network/service-tags-overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów sieciowych skojarzonych z pulami Azure Batch z Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft.Batch" i "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci pul Azure Batch.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: używanie tagów dla grup usług sieciowych (sieciowych grup zabezpieczeń) i innych zasobów związanych z zabezpieczeniami sieci i przepływem ruchu, które są skojarzone z pulami usługi Azure Batch. W przypadku poszczególnych reguł sieciowej grupy zabezpieczeń Użyj pola "Description", aby określić potrzeby biznesowe i/lub czas trwania (itp.) dla reguł zezwalających na ruch do/z sieci.

Użyj dowolnych wbudowanych definicji Azure Policy związanych ze znakiem, takich jak "Wymagaj tagu i jego wartości", aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

- [Jak utworzyć sieć wirtualną](../virtual-network/quick-create-portal.md)

- [Jak utworzyć sieciowej grupy zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z pulami Azure Batch. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view) 

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: dołączanie konta Azure Batch, aby Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia klastrowe. Wykorzystanie niestandardowych zapytań w celu wykrywania zagrożeń i reagowania na nie w środowisku.  W przypadku Azure Batch monitorowania na poziomie zasobów należy używać interfejsów API usługi Batch do monitorowania stanu zasobów lub wykonywania względem nich zapytań, takich jak zadania, zadania, węzły i pule.

- [Jak dołączyć konto Azure Batch do Azure Monitor](batch-diagnostics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: na potrzeby monitorowania na poziomie konta Azure Batch, Monitoruj każde konto usługi Batch przy użyciu funkcji usługi Azure monitor. Azure Monitor zbiera metryki i opcjonalne dzienniki diagnostyczne dla zasobów objętych zakresem na poziomie konta usługi Batch, takich jak pule, zadania i zadania. Zbieraj te dane i korzystaj z nich ręcznie lub programowo, aby monitorować działania na koncie w usłudze Batch i diagnozować problemy.

W przypadku Azure Batch monitorowania poziomu zasobów należy używać interfejsów API Azure Batch do monitorowania stanu zasobów, takich jak zadania, zadania, węzły i pule.

- [Jak skonfigurować monitorowanie i rejestrowanie na poziomie konta Azure Batch](monitoring-overview.md)

- [Informacje o monitorowaniu na poziomie zasobów w usłudze Batch](monitoring-overview.md#batch-resource-monitoring)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

#### <a name="azure-policy-built-in-definitions"></a>Azure Policy wbudowane definicje

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: Azure monitor zbiera metryki i dzienniki diagnostyczne dla zasobów na koncie usługi Azure Batch. Zbieraj te dane i korzystaj z nich na różne sposoby monitorowania konta Azure Batch i diagnozowania problemów. Możesz również skonfigurować alerty metryk, aby otrzymywać powiadomienia, gdy Metryka osiągnie określoną wartość.

W razie potrzeby można połączyć się z węzłami poszczególnych pul za pośrednictwem protokołu Secure Shell (SSH) lub Remote Desktop Protocol (RDP), aby uzyskać dostęp do dzienników lokalnych systemów operacyjnych.

- [Jak zbierać dzienniki diagnostyczne z konta usługi Azure Batch](batch-diagnostics.md#batch-diagnostics)

- [Jak zdalnie nawiązać połączenie z węzłami puli Azure Batch](/azure/batch/batch-api-basics#error-handling)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: dołączanie Azure Batch konta do Azure monitor. Upewnij się, że używany obszar roboczy platformy Azure Log Analytics ma ustawiony okres przechowywania dziennika zgodnie z regulacjami zgodności organizacji

- [Jak skonfigurować Azure Batch monitorowania i rejestrowania](monitoring-overview.md)

- [Jak skonfigurować okres przechowywania obszaru roboczego usługi Azure Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: tworzenie alertów metryk Azure Batch, które są wyzwalane, gdy wartość określonej metryki przekroczy dany próg.

- [Jak skonfigurować alerty metryk Azure Batch](batch-diagnostics.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: tworzenie alertów metryk Azure Batch, które są wyzwalane, gdy wartość określonej metryki przekroczy dany próg.

- [Jak skonfigurować alerty metryk Azure Batch](batch-diagnostics.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: Użyj usługi Windows Defender na poszczególnych węzłach usługi Batch w przypadku systemów operacyjnych Windows lub podaj własne rozwiązanie chroniące przed złośliwym oprogramowaniem, jeśli używasz systemu Linux.

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: Implementowanie rozwiązania innej firmy na potrzeby rejestrowania w systemie DNS.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: ręczne Konfigurowanie rejestrowania konsoli i transkrypcji programu PowerShell na poszczególnych węzłach.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: zachowywanie rekordu lokalnego konta administracyjnego, które jest tworzone podczas aprowizacji puli Azure Batch, a także innych utworzonych kont. Ponadto jeśli jest używana integracja Azure Active Directory, usługa Azure AD ma wbudowane role, które muszą być jawnie przypisane i dlatego queryable. Moduł Azure AD PowerShell służy do wykonywania zapytań ad hoc w celu odnajdywania kont należących do grup administracyjnych.

Ponadto możesz użyć zaleceń dotyczących zarządzania tożsamościami i dostępem Azure Security Center.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Jak monitorować tożsamość i dostęp przy użyciu Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: podczas aprowizacji puli Azure Batch można utworzyć konta komputera lokalnego. Nie ma domyślnych haseł do zmiany, ale można określić różne hasła dla dostępu Secure Shell (SSH) i Remote Desktop Protocol (RDP). Po skonfigurowaniu puli Azure Batch można wygenerować losowego użytkownika dla poszczególnych węzłów w Azure Portal lub za pośrednictwem interfejsu API Azure Resource Manager.

- [Jak dodać użytkownika do określonego węzła obliczeniowego](/rest/api/batchservice/computenode/adduser)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Zintegruj uwierzytelnianie dla aplikacji Azure Batch z Azure Active Directory. Utwórz zasady i procedury dotyczące korzystania z dedykowanych kont administracyjnych.

Ponadto możesz użyć zaleceń dotyczących zarządzania tożsamościami i dostępem Azure Security Center.

- [Jak uwierzytelniać aplikacje wsadowe za pomocą Azure Active Directory](batch-aad-auth.md)

- [Jak monitorować tożsamość i dostęp przy użyciu Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Zintegruj uwierzytelnianie dla aplikacji Azure Batch z Azure Active Directory. Włącz uwierzytelnianie wieloskładnikowe usługi Azure AD i postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem.

 

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów Azure Batch.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Jeśli masz zintegrowane uwierzytelnianie dla Azure Batch aplikacji z Azure Active Directory, Użyj raportów zabezpieczeń Azure Active Directory do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Jeśli masz zintegrowane uwierzytelnianie dla Azure Batch aplikacji z Azure Active Directory, możesz użyć nazw lokalizacji dostępu warunkowego, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory jako centralnego systemu uwierzytelniania i autoryzacji i Zintegruj uwierzytelnianie dla aplikacji Azure Batch za pomocą usługi Azure AD. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Jak uwierzytelniać aplikacje wsadowe za pomocą usługi Azure AD](batch-aad-auth.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Azure Active Directory zawiera dzienniki ułatwiające wykrywanie starych kont. Ponadto można korzystać z przeglądów dostępu do tożsamości platformy Azure w celu wydajnego zarządzania członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Tworzenie ustawień diagnostycznych dla Azure Active Directory kont użytkowników, wysyłanie dzienników inspekcji i dzienników logowania do obszaru roboczego usługi Azure log Analytics. Skonfiguruj żądane alerty w obszarze roboczym usługi Azure Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Użyj Azure Active Directory wykrywania ryzyka i funkcji ochrony tożsamości, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Dodatkowo można pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej  

**Wskazówki**: niedostępne; Azure Batch nie Skrytka klienta jeszcze obsługiwane.
 
- [Lista obsługiwanych usług Skrytka klienta](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Pule Azure Batch powinny być oddzielone przez sieć wirtualną/podsieć, odpowiednio otagowane i zabezpieczone za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Azure Batch dane powinny znajdować się w ramach zabezpieczonego konta usługi Azure Storage.

- [Jak utworzyć pulę Azure Batch w ramach Virtual Network](batch-virtual-network.md)

- [Zabezpieczanie kont usługi Azure Storage](/azure/storage/common/storage-security-guide)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: dla kont usługi Azure Storage skojarzonych z pulami Azure Batch, które zawierają informacje poufne, oznacz je jako poufne przy użyciu tagów i zabezpiecz je za pomocą najlepszych rozwiązań platformy Azure.

Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

- [Zabezpieczanie kont usługi Azure Storage](/azure/storage/common/storage-security-guide)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Szyfruj wszystkie informacje poufne podczas przesyłania. Zasoby Microsoft Azure domyślnie negocjują TLS 1,2. Upewnij się, że wszyscy klienci łączący się z pulami Azure Batch lub magazynami danych (konta usługi Azure Storage) mogą negocjować protokół TLS 1,2 lub nowszy.

Upewnij się, że do uzyskiwania dostępu do konta magazynu zawierającego dane Azure Batch jest wymagany protokół HTTPS.

- [Informacje na temat szyfrowania konta usługi Azure Storage](../storage/blobs/security-recommendations.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: dla kont usługi Azure Storage skojarzonych z pulami Azure Batch, które zawierają informacje poufne, oznacz je jako poufne przy użyciu tagów i zabezpiecz je za pomocą najlepszych rozwiązań platformy Azure.

Funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

- [Zabezpieczanie kont usługi Azure Storage](/azure/storage/common/storage-security-guide)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: stosowanie kontroli dostępu opartej na rolach w celu kontrolowania dostępu do zasobów

**Wskazówki**: Funkcja kontroli dostępu opartej na rolach (Azure RBAC) umożliwia kontrolowanie dostępu do płaszczyzny zarządzania zasobów platformy Azure, w tym konta usługi Batch, pul wsadowych i kont magazynu.

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla usługi Azure Storage i zasobów obliczeniowych. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: dla kont magazynu skojarzonych z kontem Azure Batch zaleca się, aby firma Microsoft mogła zarządzać kluczami szyfrowania, jednak istnieje możliwość zarządzania własnymi kluczami w razie potrzeby.

Usługa Azure Disk Encryption może służyć do ochrony i zabezpieczania danych w celu spełnienia wymagań dotyczących zabezpieczeń i zgodności w organizacji. Wszystkie dyski zarządzane, migawki, obrazy i dane zapisywane na istniejących dyskach są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez platformę.

- [Jak zarządzać kluczami szyfrowania dla kont usługi Azure Storage](/azure/storage/common/storage-encryption-keys-portal)

- [Jak skonfigurować zarządzane przez klienta klucze szyfrowania](/azure/storage/common/storage-encryption-keys-portal)

- [Jak utworzyć pulę z włączonym szyfrowaniem dysków](disk-encryption.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące sytuacji, w których zmiany dotyczą najważniejszych zasobów platformy Azure związanych z kontami Azure Batch/pulami lub skojarzonych z nimi.

Skonfiguruj ustawienia diagnostyczne dla kont magazynu skojarzonych z pulą Azure Batch, aby monitorować i rejestrować wszystkie operacje CRUD na danych puli.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md)

- [Jak włączyć dodatkowe rejestrowanie/inspekcję dla konta usługi Azure Storage](../storage/common/storage-monitor-storage-account.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: dla węzłów puli Azure Batch użytkownik jest odpowiedzialny za zarządzanie rozwiązaniem do zarządzania lukami w zabezpieczeniach.

Opcjonalnie, jeśli istnieje Rapid7, Qualys lub jakakolwiek inna subskrypcja platformy do zarządzania lukami w zabezpieczeniach, można ręcznie zainstalować agentów oceny luk w zabezpieczeniach w węzłach puli usługi Batch i zarządzać węzłami za pomocą odpowiedniego portalu.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: Firma Microsoft do obsługi i aktualizacji obrazów węzła podstawowego puli Azure Batch. Upewnij się, że system operacyjny węzłów puli Azure Batch jest zastosowany na czas trwania okresu istnienia klastra, co może wymagać włączenia aktualizacji automatycznych, monitorowania węzłów lub wykonywania okresowych ponownych uruchomień.

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Współużytkowane

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: Upewnij się, że węzły puli Azure Batch "aplikacje innych firm pozostają poprawione na czas istnienia klastra, co może wymagać włączenia aktualizacji automatycznych, monitorowania węzłów lub wykonywania okresowego ponownego rozruchu.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: Jeśli masz Rapid7, Qualys lub inną subskrypcję platformy do zarządzania lukami w zabezpieczeniach, możesz użyć portalu tego dostawcy, aby wyświetlić i porównać przeskanowane luki w zabezpieczeniach.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj typowego programu oceny ryzyka (np. typowego oceniania luk w zabezpieczeniach systemu) lub domyślnych ocen ryzyka udostępnianych przez narzędzie skanowania innej firmy.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (np. obliczeń, magazynu, sieci itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem Eksploratora Azure Resource Graph, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](/azure/billing/billing-create-subscription)

- [Jak utworzyć Grupy zarządzania](/azure/governance/management-groups/create)

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Definiowanie listy zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Użyj grafu zasobów platformy Azure, aby wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: dla węzłów puli Azure Batch należy wdrożyć rozwiązanie innych firm do monitorowania węzłów klastra dla niezatwierdzonych aplikacji.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: dla węzłów puli Azure Batch należy wdrożyć rozwiązanie innych firm do monitorowania węzłów klastra dla niezatwierdzonych aplikacji.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: dla węzłów puli Azure Batch należy wdrożyć rozwiązanie innych firm, aby zapobiec wykonywaniu nieautoryzowanego oprogramowania.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad: 
- Niedozwolone typy zasobów 
- Dozwolone typy zasobów 

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: dla węzłów puli Azure Batch należy wdrożyć rozwiązanie innych firm, aby zapobiec wykonywaniu nieautoryzowanych typów plików.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft.Batch", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji kont i pul Azure Batch.

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: Ustanów bezpieczne konfiguracje dla systemu operacyjnego węzłów puli usługi Batch.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia zasobów platformy Azure związanych z Twoim kontem i pulami usługi Batch (na przykład sieciami wirtualnymi, podsieciami, zaporami platformy Azure, kontami magazynu i magazynem). Do tworzenia zasad niestandardowych można użyć aliasów Azure Policy z następujących przestrzeni nazw:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: Azure Batch obrazów systemu operacyjnego puli zarządzanych i obsługiwanych przez firmę Microsoft. Użytkownik jest odpowiedzialny za implementację konfiguracji stanu na poziomie systemu operacyjnego.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla kont Azure Batch, pul lub powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentacja Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: Jeśli używasz niestandardowych obrazów dla pul Azure Batch, użyj kontroli dostępu opartej na ROLACH (RBAC), aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom.

- [Zrozumienie RBAC na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Jak skonfigurować kontrolę RBAC na platformie Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj wbudowanych definicji Azure Policy do alertów, inspekcji i wymuszania konfiguracji zasobów związanych z Azure Batch.  Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft.Batch", aby utworzyć niestandardowe zasady dla kont Azure Batch i pul. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu utrzymania żądanego stanu dla systemów operacyjnych węzłów puli Azure Batch.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft.Batch", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji wystąpienia Azure Batch. Można również używać wszelkich wbudowanych zasad utworzonych specjalnie dla Azure Batch lub zasobów używanych przez Azure Batch, takich jak:
- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń — konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej
- Należy włączyć dzienniki diagnostyczne na kontach wsadowych

- [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: Implementowanie rozwiązania innej firmy w celu monitorowania stanu systemów operacyjnych węzłów puli Azure Batch.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Azure Key Vault mogą być używane z wdrożeniami Azure Batch do zarządzania kluczami magazynu puli w ramach kont usługi Azure Storage.

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Azure Key Vault](../key-vault/general/quick-create-portal.md)

- [Jak przeprowadzić uwierzytelnianie w Key Vault](../key-vault/general/authentication.md)
- [Jak przypisać zasady dostępu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault. 

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: korzystanie z usługi Windows Defender na poszczególnych węzłach puli Azure Batch w przypadku systemów operacyjnych Windows lub w przypadku korzystania z systemu Linux przy użyciu własnego rozwiązania chroniącego przed złośliwym oprogramowaniem.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Batch), ale nie jest uruchamiane na zawartości klienta.

Skanuj wstępnie wszystkie pliki przekazywane do zasobów platformy Azure, które nie są obliczeniowe, takie jak App Service, Data Lake Storage, Blob Storage itd. Firma Microsoft nie może uzyskać dostępu do danych klienta w tych wystąpieniach.

- [Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: Użyj programu Windows Defender na poszczególnych węzłach puli Azure Batch w przypadku systemów operacyjnych Windows i upewnij się, że funkcja automatycznej aktualizacji jest włączona. Zapewniaj własne rozwiązanie chroniące przed złośliwym oprogramowaniem, jeśli używasz systemu Linux.

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: w przypadku korzystania z konta usługi Azure Storage na potrzeby magazynu danych puli Azure Batch wybierz odpowiednią opcję NADMIAROWOŚCI (LRS, ZRS, GRS, RA-GRS). 

- [Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage](../storage/common/storage-redundancy.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: w przypadku korzystania z konta usługi Azure Storage na potrzeby magazynu danych puli Azure Batch wybierz odpowiednią opcję NADMIAROWOŚCI (LRS, ZRS, GRS, RA-GRS). W przypadku korzystania z Azure Key Vault dla dowolnej części wdrożenia Azure Batch upewnij się, że utworzono kopię zapasową kluczy.

- [Jak skonfigurować nadmiarowość magazynu dla kont usługi Azure Storage](../storage/common/storage-redundancy.md)

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: Jeśli zarządzasz własnymi kluczami dla kont usługi Azure Storage lub innych zasobów związanych z implementacją Azure Batch, okresowo Przetestuj przywracanie kluczy kopii zapasowej.

- [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Jak przywrócić klucz zarządzany przez klienta za pomocą programu PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Jeśli Azure Key Vault są używane do przechowywania kluczy związanych z kontami magazynu puli Azure Batch, Włącz Soft-Delete w Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

- [Jak włączyć usuwanie nietrwałe w Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Upewnij się, że istnieją plany odpowiedzi na zdarzenia, które definiują role pracowników, a także fazy obsługi zdarzeń/zarządzania.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu uzyskać dostęp. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe o zdarzeniach dotyczących zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną osobę.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: — [postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

Aby uzyskać więcej informacji na temat strategii i sposobu działania tworzenia zespołu Red-and-testowego na żywo w ramach infrastruktury chmurowej, usług i aplikacji firmy Microsoft, poniżej: 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Współużytkowane

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)

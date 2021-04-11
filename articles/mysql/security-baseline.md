---
title: Podstawa zabezpieczeń platformy Azure dla Azure Database for MySQL
description: Linia bazowa zabezpieczeń Azure Database for MySQL zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: mysql
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 62501d6174b9ef8806de0bbdae0dfedfe70da03a
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968080"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Podstawa zabezpieczeń platformy Azure dla Azure Database for MySQL

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 1,0](../security/benchmarks/overview-v1.md) do Azure Database for MySQL. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Azure Database for MySQL. Nie zostały wykluczone żadne **kontrolki** , które mają zastosowanie do Azure Database for MySQL lub dla których odpowiedzialnością jest firma Microsoft.

Aby dowiedzieć się, jak Azure Database for MySQL całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Azure Database for MySQL pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Konfigurowanie prywatnego linku dla Azure Database for MySQL z prywatnymi punktami końcowymi. Usługa Private Link umożliwia łączenie z różnymi usługami PaaS na platformie Azure za pośrednictwem prywatnego punktu końcowego. Usługa Azure Private Link zasadniczo łączy usługi platformy Azure z Twoją prywatną siecią wirtualną. Ruch między siecią wirtualną i wystąpieniem MySQL podróżuje z siecią szkieletową firmy Microsoft.

Alternatywnie możesz użyć punktów końcowych usługi Virtual Network do ochrony i ograniczania dostępu sieciowego do implementacji Azure Database for MySQL. Reguły sieci wirtualnej to jedna funkcja zabezpieczeń zapory, która kontroluje, czy serwer Azure Database for MySQL akceptuje komunikację wysyłaną z określonych podsieci w sieciach wirtualnych.

Możesz również zabezpieczyć serwer Azure Database for MySQL przy użyciu reguł zapory. Zapora serwera uniemożliwia dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

- [Jak skonfigurować link prywatny dla Azure Database for MySQL](howto-configure-privatelink-portal.md)

- [Tworzenie punktów końcowych usługi sieci wirtualnej i reguł sieci wirtualnej w programie Azure Database for MySQL oraz zarządzanie nimi](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

- [Jak skonfigurować reguły zapory Azure Database for MySQL](howto-manage-firewall-using-portal.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i interfejsów sieciowych

**Wskazówki**: gdy wystąpienie Azure Database for MySQL jest zabezpieczone do prywatnego punktu końcowego, można wdrożyć maszyny wirtualne w tej samej sieci wirtualnej. Za pomocą sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) można ograniczyć ryzyko związane z eksfiltracji danych. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak skonfigurować link prywatny dla Azure Database for MySQL](howto-configure-privatelink-portal.md)

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi niezłośliwymi adresami IP

**Wskazówki**: Użyj zaawansowanej ochrony przed zagrożeniami dla Azure Database for MySQL. Zaawansowana ochrona przed zagrożeniami wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Włącz DDoS Protection standard w sieciach wirtualnych skojarzonych z wystąpieniami Azure Database for MySQL, aby chronić przed atakami DDoS. Użyj Azure Security Center zintegrowanej analizy zagrożeń, aby odmówić komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

- [Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Jak skonfigurować ochronę DDoS](../ddos-protection/manage-ddos-protection.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: gdy wystąpienie Azure Database for MySQL jest zabezpieczone do prywatnego punktu końcowego, można wdrożyć maszyny wirtualne w tej samej sieci wirtualnej. Następnie można skonfigurować grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń) w celu zmniejszenia ryzyka związanego z eksfiltracji danych. Włącz dzienniki przepływu sieciowej grupy zabezpieczeń i Wyślij dzienniki do konta magazynu na potrzeby inspekcji ruchu. Możesz również wysłać dzienniki przepływu sieciowej grupy zabezpieczeń do obszaru roboczego Log Analytics i użyć Analiza ruchu, aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Niektóre zalety Analiza ruchu to możliwość wizualizacji aktywności sieciowej i identyfikowania aktywnych punktów, identyfikowania zagrożeń bezpieczeństwa, zrozumienia wzorców przepływu ruchu i wyznaczania konfiguracji sieci.

- [Jak włączyć dzienniki przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Użyj zaawansowanej ochrony przed zagrożeniami dla Azure Database for MySQL. Zaawansowana ochrona przed zagrożeniami wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

- [Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Database for MySQL](howto-database-threat-protection-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: dla zasobów, które wymagają dostępu do wystąpień Azure Database for MySQL, użyj Virtual Network tagów usługi, aby zdefiniować kontrolę dostępu do sieci w grupach zabezpieczeń sieci lub w zaporze platformy Azure. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. SQL. Zachodnie) w odpowiednim polu źródłowym lub docelowym reguły można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy.

Uwaga: Azure Database for MySQL używa tagów usługi "Microsoft. SQL".

- [Aby uzyskać więcej informacji na temat używania tagów usługi](../virtual-network/service-tags-overview.md)

- [Opis użycia tagu usługi dla Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych i zasobów sieciowych skojarzonych z wystąpieniami Azure Database for MySQL przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeniach nazw "Microsoft. DBforMySQL" i "Microsoft. Network", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci wystąpień Azure Database for MySQL. Możesz również korzystać z wbudowanych definicji zasad związanych z siecią lub Azure Database for MySQL wystąpieniami, takimi jak:

- Należy włączyć Standard DDoS Protection

- Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Przykłady Azure Policy dla sieci](/azure/governance/policy/samples/)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Użyj tagów dla zasobów związanych z zabezpieczeniami sieci i przepływem ruchu dla wystąpień Azure Database for MySQL, aby zapewnić metadane i organizację logiczną.

Użyj dowolnych wbudowanych definicji Azure Policy związanych z tagowaniem, takich jak **Wymagaj znacznika i jego wartości** , aby upewnić się, że wszystkie zasoby są tworzone przy użyciu tagów i powiadomienia o istniejących nieoznakowanych zasobach.

Możesz użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wyszukiwać lub wykonywać akcje na zasobach na podstawie ich tagów.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian zasobów sieciowych związanych z wystąpieniami Azure Database for MySQL. Tworzenie alertów w ramach Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w krytycznych zasobach sieciowych.

- [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Jak utworzyć alerty w Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: Włączanie ustawień diagnostycznych i dzienników serwera i dzienników pozyskiwania w celu agregowania danych zabezpieczeń wygenerowanych przez wystąpienia Azure Database for MySQL. W Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

- [Informacje o dziennikach serwera dla Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włączanie ustawień diagnostycznych w wystąpieniach Azure Database for MySQL w celu uzyskania dostępu do dzienników metryk inspekcji, powolnych zapytań i MySQL. Upewnij się, że został jawnie włączony dziennik inspekcji programu MySQL. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy. Możesz również włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure i wysłać dzienniki do tego samego obszaru roboczego Log Analytics lub konta magazynu.

- [Informacje o dziennikach serwera dla Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Jak skonfigurować i uzyskać dostęp do dzienników wolnych zapytań dla Azure Database for MySQL](howto-configure-server-logs-in-portal.md)

- [Jak skonfigurować i uzyskać dostęp do dzienników inspekcji dla Azure Database for MySQL](howto-configure-audit-logs-portal.md)

- [Jak skonfigurować ustawienia diagnostyczne dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor w obszarze roboczym log Analytics używanym do przechowywania dzienników Azure Database for MySQL należy ustawić okres przechowywania zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

- [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Przechowywanie dzienników zasobów na koncie usługi Azure Storage](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs#send-to-azure-storage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników z wystąpień Azure Database for MySQL w celu nietypowego zachowania. Użyj Log Analytics Azure Monitor, aby przejrzeć dzienniki i wykonywać zapytania dotyczące danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Aby uzyskać więcej informacji na temat Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Włącz zaawansowaną ochronę przed zagrożeniami dla Azure Database for MySQL. Zaawansowana ochrona przed zagrożeniami wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Ponadto można włączyć Dzienniki serwera i ustawienia diagnostyczne dla programu MySQL oraz wysyłać dzienniki do obszaru roboczego Log Analytics. Dołącz obszar roboczy Log Analytics do usługi Azure o, ponieważ zapewnia ona rozwiązanie do automatycznej reakcji aranżacji zabezpieczeń (). Pozwala to na tworzenie i używanie automatycznych rozwiązań elementy PlayBook w celu korygowania problemów z zabezpieczeniami.

- [Jak włączyć zaawansowaną ochronę przed zagrożeniami dla Azure Database for MySQL (wersja zapoznawcza)](howto-database-threat-protection-portal.md)

- [Informacje o dziennikach serwera dla Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Jak skonfigurować i uzyskać dostęp do dzienników wolnych zapytań dla Azure Database for MySQL](howto-configure-server-logs-in-portal.md)

- [Jak skonfigurować i uzyskać dostęp do dzienników inspekcji dla Azure Database for MySQL](howto-configure-audit-logs-portal.md)

- [Jak skonfigurować ustawienia diagnostyczne dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: przechowywanie spisu kont użytkowników, które mają dostęp administracyjny do płaszczyzny zarządzania (np. Azure Portal) usługi Azure Database for MySQLinstances. Ponadto należy zachować spis kont administracyjnych, które mają dostęp do płaszczyzny danych (w samej bazie danych) wystąpień Azure Database for MySQL. (Podczas tworzenia serwera MySQL należy podać poświadczenia dla użytkownika administratora. Ten administrator może służyć do tworzenia dodatkowych użytkowników programu MySQL.

Azure Database for MySQL nie obsługuje wbudowanej kontroli dostępu opartej na rolach, ale można tworzyć role niestandardowe na podstawie określonych opcji dostawcy zasobów.

- [Informacje o rolach niestandardowych dla subskrypcji platformy Azure](../role-based-access-control/custom-roles.md) 

- [Informacje o operacjach dostawcy zasobów Azure Database for MySQL](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql)

- [Informacje na temat zarządzania dostępem Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-security#access-management)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Active Directory (Azure AD) nie ma koncepcji domyślnych haseł.

Po utworzeniu samego zasobu Azure Database for MySQL platforma Azure wymusza tworzenie użytkownika administracyjnego przy użyciu silnego hasła. Jednak po utworzeniu wystąpienia programu MySQL można użyć pierwszego utworzonego konta administratora serwera, aby utworzyć dodatkowych użytkowników i udzielić im dostępu administracyjnego. Podczas tworzenia tych kont należy skonfigurować inne, silne hasło dla każdego konta.

- [Jak utworzyć dodatkowe konta dla Azure Database for MySQL](howto-create-users.md)

- [Jak zaktualizować hasło administratora](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących używania dedykowanych kont administracyjnych, które mają dostęp do wystąpień Azure Database for MySQL. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych.

- [Informacje o tożsamości i dostępie Azure Security Center](../security-center/security-center-identity-access.md)

- [Informacje na temat tworzenia użytkowników administracyjnych w Azure Database for MySQL](howto-create-users.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Użyj Azure Active Directory logowania jednokrotnego (SSO)

**Wskazówki**: logowanie do Azure Database for MySQL jest obsługiwane zarówno przy użyciu nazwy użytkownika, jak i hasła skonfigurowanej bezpośrednio w bazie danych, a także przy użyciu tożsamości usługi Azure Active Directory (Azure AD) i korzystania z tokenu usługi Azure AD w celu nawiązania połączenia. W przypadku korzystania z tokenu usługi Azure AD obsługiwane są różne metody, takie jak użytkownik usługi Azure AD, Grupa usługi Azure AD lub aplikacja usługi Azure AD łącząca się z bazą danych.

Oddzielnie dostęp do płaszczyzny kontroli dla bazy danych MySQL jest dostępny za pośrednictwem interfejsu API REST i obsługuje logowanie jednokrotne. Aby przeprowadzić uwierzytelnianie, należy ustawić nagłówek autoryzacji dla żądań na token internetowy JSON uzyskany z usługi Azure AD.

- [Korzystanie z usługi Azure AD do uwierzytelniania za pomocą Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Informacje o interfejsie API REST Azure Database for MySQL](/rest/api/mysql/)

- [Opis logowania jednokrotnego w usłudze Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego dla wszystkich Azure Active Directory dostępu opartego na usłudze

**Wskazówki**: Włączanie uwierzytelniania wieloskładnikowego Azure Active Directory (Azure AD) i przestrzeganie Azure Security Center zalecenia dotyczące zarządzania tożsamościami i dostępem. Przy użyciu tokenów usługi Azure AD do logowania się do bazy danych można wymagać uwierzytelniania wieloskładnikowego na potrzeby logowania do bazy danych.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Korzystanie z usługi Azure AD do uwierzytelniania za pomocą Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: Włącz zaawansowaną ochronę przed zagrożeniami dla Azure Database for MySQL, aby generować alerty dla podejrzanych działań.

Ponadto można użyć usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Użyj funkcji wykrywania ryzyka usługi Azure AD, aby wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

- [Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Omówienie wykrywania ryzyka usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby umożliwić dostęp do portalu i Azure Resource Manager tylko z określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

W celu zalogowania się do Azure Database for MySQL zaleca się używanie usługi Azure AD i korzystanie z tokenu usługi Azure AD w celu nawiązania połączenia. W przypadku korzystania z tokenu usługi Azure AD obsługiwane są różne metody, takie jak użytkownik usługi Azure AD, Grupa usługi Azure AD lub aplikacja usługi Azure AD łącząca się z bazą danych.

Poświadczeń usługi Azure AD można także używać do administrowania na poziomie płaszczyzny zarządzania (np. Azure Portal) w celu kontrolowania kont administratorów MySQL.

- [Korzystanie z usługi Azure AD do uwierzytelniania za pomocą Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Zapoznaj się z dziennikami usługi Azure Active Directory (Azure AD), aby ułatwić odnalezienie starych kont, które mogą obejmować te z Azure Database for MySQL rolami administracyjnymi. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, uzyskiwać dostęp do aplikacji firmowych, które mogą być używane do uzyskiwania dostępu do Azure Database for MySQL i przypisań ról. Dostęp użytkowników powinien być regularnie przeglądany, na przykład co 90 dni, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Włączanie ustawień diagnostycznych dla Azure Database for MySQL i Azure Active Directory (Azure AD), wysyłanie wszystkich dzienników do obszaru roboczego log Analytics. Skonfiguruj żądane alerty (takie jak nieudane próby uwierzytelniania) w ramach Log Analytics.

- [Jak skonfigurować i uzyskać dostęp do dzienników wolnych zapytań dla Azure Database for MySQL](/Azure/mysql/howto-configure-server-logs-in-portal)

- [Jak skonfigurować i uzyskać dostęp do dzienników inspekcji dla Azure Database for MySQL](/Azure/mysql/howto-configure-audit-logs-portal)

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Włącz zaawansowaną ochronę przed zagrożeniami dla Azure Database for MySQL, aby generować alerty dla podejrzanych działań.

Użyj funkcji ochrony tożsamości i wykrywania ryzyka Azure Active Directory (Azure AD), aby skonfigurować automatyczne reagowanie na wykryte podejrzane działania. Automatyczne odpowiedzi można włączyć za pomocą wskaźnikowego platformy Azure, aby zaimplementować odpowiedzi na zabezpieczenia organizacji.

Możesz również pozyskiwanie dzienników na platformie Azure — wskaźnik do dalszych badań.

- [Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Omówienie Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu wystąpień Azure Database for MySQL lub związanych z nimi zasobów, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Użyj połączenia prywatnego, punktów końcowych usługi i/lub reguł zapory, aby wyizolować i ograniczyć dostęp sieciowy do wystąpień Azure Database for MySQL.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Jak skonfigurować link prywatny dla Azure Database for MySQL](concepts-data-access-security-private-link.md)

- [Tworzenie punktów końcowych usługi sieci wirtualnej i reguł sieci wirtualnej w programie Azure Database for MySQL oraz zarządzanie nimi](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

- [Jak skonfigurować reguły zapory Azure Database for MySQL](concepts-firewall-rules.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: w przypadku korzystania z maszyn wirtualnych platformy Azure w celu uzyskania dostępu do wystąpień Azure Database for MySQL należy skorzystać z linku prywatnego, konfiguracji sieci MySQL, sieciowych grup zabezpieczeń i tagów usług w celu ograniczenia możliwości eksfiltracji danych.

Firma Microsoft zarządza podstawową infrastrukturą dla Azure Database for MySQL i ma zaimplementowane ścisłe kontrole, aby zapobiec utracie lub narażeniu danych klientów.

- [Jak wyeliminować eksfiltracji danych dla Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention)

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Azure Database for MySQL obsługuje łączenie serwera MySQL z aplikacjami klienckimi przy użyciu Secure SOCKETS Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją. W Azure Portal upewnij się, że "Wymuszaj połączenie SSL" jest domyślnie włączone dla wszystkich wystąpień Azure Database for MySQL.

Obecnie wersje protokołu TLS obsługiwane przez Azure Database for MySQL to TLS 1,0, TLS 1,1, TLS 1,2.

- [Jak skonfigurować szyfrowanie podczas przesyłania dla Azure Database for MySQL](concepts-ssl-connection-security.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji, klasyfikacji i zapobiegania utracie danych nie są jeszcze dostępne dla Azure Database for MySQL. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../security/fundamentals/protection-customer-data.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów 

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby kontrolować dostęp do płaszczyzny kontroli Azure Database for MySQL (np. Azure Portal). Aby uzyskać dostęp do płaszczyzny danych (w samej bazie danych), należy użyć zapytań SQL do tworzenia użytkowników i konfigurowania uprawnień użytkownika. Kontrola RBAC platformy Azure nie ma wpływu na uprawnienia użytkowników w ramach bazy danych.

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Jak skonfigurować dostęp użytkowników przy użyciu programu SQL dla Azure Database for MySQL](howto-create-users.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych Azure Database for MySQL i innych krytycznych lub powiązanych zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Postępuj zgodnie z zaleceniami Azure Security Center na zabezpieczanie Azure Database for MySQL i powiązanych zasobów.

Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Azure Database for MySQL.

- [Informacje na temat Azure Security Center zaleceń](../security-center/recommendations-reference.md)

- [Pokrycie funkcji dla usług Azure PaaS Services w Azure Security Center](../security-center/features-paas.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym wystąpień Azure Database for MySQL) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj znaczniki do Azure Database for MySQL wystąpień i innych powiązanych zasobów, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie jest to konieczne, do organizowania i śledzenia wystąpień Azure Database for MySQL i powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych i platformy Azure jako całości.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto za pomocą grafu zasobów platformy Azure można wysyłać zapytania o zasoby w ramach subskrypcji i odnajdywać je.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które mogą być tworzone w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](/azure/governance/policy/samples/index)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". Może to uniemożliwić tworzenie i wprowadzanie zmian w zasobach w środowisku wysokiego poziomu zabezpieczeń, takich jak wystąpienia Azure Database for MySQL zawierające informacje poufne.

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień Azure Database for MySQL przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw **Microsoft. DBforMySQL** , aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji sieci wystąpień Azure Database for MySQL. Możesz również używać wbudowanych definicji zasad związanych z wystąpieniami Azure Database for MySQL, takimi jak:

Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji Azure Policy dla wystąpień Azure Database for MySQL i powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentacja Azure Repos](/azure/devops/repos/)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. DBforMySQL", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw **Microsoft. DBforMySQL** , aby utworzyć zasady niestandardowe do alertu, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla wystąpień Azure Database for MySQL i powiązanych zasobów.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: dla Virtual Machines platformy Azure lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do Azure Database for MySQL wystąpień należy użyć tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć Azure Database for MySQL zarządzaniem kluczami tajnymi. Upewnij się, że Key Vault usuwanie trwałe jest włączone.

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Jak utworzyć Key Vault](../key-vault/general/quick-create-portal.md)

- [Jak zapewnić uwierzytelnianie Key Vault przy użyciu tożsamości zarządzanej](../key-vault/general/assign-access-policy-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: wystąpienie Azure Database for MySQL obsługuje uwierzytelnianie Azure Active Directory (Azure AD) w celu uzyskiwania dostępu do baz danych. Podczas tworzenia wystąpienia Azure Database for MySQL podajesz poświadczenia dla użytkownika administratora. Ten administrator może służyć do tworzenia dodatkowych użytkowników bazy danych.

W przypadku usługi Azure Virtual Machines lub aplikacji sieci Web działających na Azure App Service używanym do uzyskiwania dostępu do Azure Database for MySQL wystąpień Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault do przechowywania i pobierania poświadczeń dla wystąpienia Azure Database for MySQL. Upewnij się, że Key Vault usuwanie trwałe jest włączone.

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure AD. Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak skonfigurować tożsamości zarządzane](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Database for MySQL), ale nie jest ono uruchamiane w treści klienta.

Przed przeskanowaniem zawartość przekazywana do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage, Azure Database for MySQL itp. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zapewnianie regularnych zautomatyzowanych kopii zapasowych

**Wskazówki**: Azure Database for MySQL wykonuje kopie zapasowe plików danych i dziennika transakcji. W zależności od obsługiwanego maksymalnego rozmiaru magazynu należy wykonać pełne i różnicowe kopie zapasowe (maksymalnie 4 TB serwerów magazynu) lub kopie zapasowe migawek (maksymalnie 16 TB serwerów magazynu). Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w ramach skonfigurowanego okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Opcjonalnie można skonfigurować ją do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES.

- [Informacje o kopiach zapasowych dla Azure Database for MySQL](concepts-backup.md)

- [Opis konfiguracji początkowej Azure Database for MySQL](tutorial-design-database-using-portal.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: Azure Database for MySQL automatycznie tworzy kopie zapasowe serwera i przechowuje je w magazynie lokalnie nadmiarowym lub geograficznie nadmiarowym, zgodnie z wyborem użytkownika. Kopie zapasowe mogą być używane do przywracania serwera do punktu w czasie. Tworzenie kopii zapasowych i przywracanie jest istotną częścią strategii ciągłości biznesowej, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem. 

W przypadku używania Azure Key Vault do przechowywania poświadczeń dla wystąpień Azure Database for MySQL należy zapewnić regularne automatyczne tworzenie kopii zapasowych kluczy. 

- [Informacje o kopiach zapasowych dla Azure Database for MySQL](howto-restore-server-portal.md) 

- [Jak utworzyć kopię zapasową kluczy Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: w Azure Database for MySQL wykonanie przywracania powoduje utworzenie nowego serwera na podstawie kopii zapasowych oryginalnego serwera. Dostępne są dwa typy przywracania: Przywracanie do punktu w czasie i przywracanie geograficzne. Przywracanie do punktu w czasie jest dostępne z opcją nadmiarowości kopii zapasowych i tworzy nowy serwer w tym samym regionie, w którym znajduje się oryginalny serwer. Przywracanie geograficzne jest dostępne tylko wtedy, gdy skonfigurowano serwer dla magazynu geograficznie nadmiarowego i umożliwia przywrócenie serwera do innego regionu.

Szacowany czas odzyskiwania zależy od kilku czynników, takich jak rozmiary bazy danych, rozmiar dziennika transakcji, przepustowość sieci i łączna liczba baz danych, które są odzyskiwane w tym samym regionie w tym samym czasie. Czas odzyskiwania jest zwykle krótszy niż 12 godzin.

Okresowe testowanie przywracania wystąpień Azure Database for MySQL.

- [Informacje na temat tworzenia kopii zapasowych i przywracania w programie Azure Database for MySQL](concepts-backup.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Azure Database for MySQL pobiera pełne, różnicowe i transakcyjne kopie zapasowe dziennika. Te kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w ramach skonfigurowanego okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Opcjonalnie można skonfigurować ją do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES. Upewnij się, że Key Vault usuwanie trwałe jest włączone.

- [Informacje na temat tworzenia kopii zapasowych i przywracania w programie Azure Database for MySQL](concepts-backup.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizach używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które doprowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z regułami testowania penetracji Microsoft Cloud, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)

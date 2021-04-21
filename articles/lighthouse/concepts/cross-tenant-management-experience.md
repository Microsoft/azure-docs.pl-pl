---
title: Środowiska zarządzania wieloma dzierżawami
description: Zarządzanie delegowanymi zasobami platformy Azure umożliwia zarządzanie między dzierżawami.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 027d1d5e81d5a652a7e2d5441c40440c661f730f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778629"
---
# <a name="cross-tenant-management-experiences"></a>Środowiska zarządzania wieloma dzierżawami

Jako dostawca usług możesz używać usługi Azure Lighthouse do [zarządzania](../overview.md) zasobami dla wielu klientów z twojej własnej dzierżawy Azure Active Directory (Azure AD). Wiele zadań i usług można wykonywać w dzierżawach zarządzanych przy użyciu zarządzania [zasobami delegowanymi platformy Azure.](../concepts/azure-delegated-resource-management.md)

> [!TIP]
> Zarządzania zasobami delegowanymi na platformie Azure można również używać w przedsiębiorstwie, które ma wiele własnych dzierżaw usługi [Azure AD,](enterprise.md) aby uprościć administrowanie wieloma dzierżawami.

## <a name="understanding-tenants-and-delegation"></a>Informacje o dzierżawach i delegowaniu

Dzierżawa usługi Azure AD jest reprezentacją organizacji. Jest to dedykowane wystąpienie usługi Azure AD, które organizacja otrzymuje po utworzeniu relacji z firmą Microsoft przez zarejestrowanie się na platformie Azure, Microsoft 365 lub innych usługach. Każda dzierżawa usługi Azure AD jest odrębna i oddzielona od innych dzierżaw usługi Azure AD i ma własny identyfikator dzierżawy (identyfikator GUID). Aby uzyskać więcej informacji, [zobacz Co to jest Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Zazwyczaj w celu zarządzania zasobami platformy Azure dla klienta dostawcy usług muszą zalogować się do usługi Azure Portal przy użyciu konta skojarzonego z dzierżawą tego klienta, co wymaga od administratora w dzierżawie klienta utworzenia kont użytkowników dla dostawcy usług i zarządzania nimi.

W Azure Lighthouse proces dołączania określa użytkowników w dzierżawie dostawcy usług, którzy będą mogli pracować z delegowanymi subskrypcjami i grupami zasobów w dzierżawie klienta. Ci użytkownicy mogą następnie zalogować się do Azure Portal przy użyciu własnych poświadczeń. W ramach Azure Portal mogą zarządzać zasobami należącymi do wszystkich klientów, do których mają dostęp. Można to zrobić, [](../how-to/view-manage-customers.md) odwiedzając stronę Moi klienci w skrypcie Azure Portal lub pracując bezpośrednio w kontekście subskrypcji tego klienta, w skrypcie Azure Portal lub za pośrednictwem interfejsów API.

Azure Lighthouse zapewnia większą elastyczność zarządzania zasobami dla wielu klientów bez konieczności logowania się do różnych kont w różnych dzierżawach. Na przykład dostawca usług może mieć dwóch klientów z różnymi zakresami odpowiedzialności i poziomami dostępu. Korzystając Azure Lighthouse, autoryzowani użytkownicy mogą zalogować się do dzierżawy dostawcy usług w celu uzyskania dostępu do tych zasobów.

![Diagram przedstawiający zasoby klientów zarządzane za pośrednictwem jednej dzierżawy dostawcy usług.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Obsługa interfejsów API i narzędzi do zarządzania

Zadania zarządzania dotyczące delegowanych zasobów można wykonywać bezpośrednio w portalu lub przy użyciu interfejsów API i narzędzi do zarządzania (takich jak interfejs wiersza polecenia platformy Azure i Azure PowerShell). Wszystkie istniejące interfejsy API mogą być używane podczas pracy z delegowanymi zasobami, o ile funkcje są obsługiwane w przypadku zarządzania między dzierżawami, a użytkownik ma odpowiednie uprawnienia.

Polecenie [Azure PowerShell cmdlet Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) domyślnie wyświetla wartość dla `TenantId` dzierżawy zarządzającej. Możesz użyć atrybutów i dla każdej subskrypcji, aby określić, czy zwracana subskrypcja należy do dzierżawy zarządzanej, czy `HomeTenantId` `ManagedByTenantIds` do dzierżawy zarządzającej.

Podobnie polecenia interfejsu wiersza polecenia platformy Azure, takie [jak az account list,](/cli/azure/account#az_account_list) pokazują `homeTenantId` atrybuty i `managedByTenants` . Jeśli nie widzisz tych wartości podczas korzystania z interfejsu wiersza polecenia platformy Azure, spróbuj wyczyścić pamięć podręczną, uruchamiając polecenia `az account clear` , a następnie `az login --identity` .

W interfejsie API REST platformy Azure polecenia [Subskrypcje — Pobierz](/rest/api/resources/subscriptions/get) i [Subskrypcje — Lista](/rest/api/resources/subscriptions/list) obejmują polecenie `ManagedByTenant` .

> [!NOTE]
> Oprócz informacji o dzierżawie związanych z Azure Lighthouse, dzierżawy wyświetlane przez te interfejsy API mogą również odzwierciedlać dzierżawy partnerów dla aplikacji Azure Databricks zarządzanych przez platformę Azure.

Zapewniamy również interfejsy API, które są specyficzne dla wykonywania Azure Lighthouse zadań. Aby uzyskać więcej informacji, zobacz **sekcję Informacje.**

## <a name="enhanced-services-and-scenarios"></a>Ulepszone usługi i scenariusze

Większość zadań i usług można wykonywać w ramach delegowanych zasobów w obrębie zarządzanych dzierżawców. Poniżej przedstawiono niektóre kluczowe scenariusze, w których zarządzanie między dzierżawami może być szczególnie skuteczne.

[Azure Arc:](../../azure-arc/index.yml)

- Zarządzanie serwerami hybrydowymi na dużą [skalę — Azure Arc serwery z włączoną obsługą usługi](../../azure-arc/servers/overview.md):
  - [Zarządzanie maszynami z systemem Windows Server lub Linux poza](../../azure-arc/servers/onboard-portal.md) platformą Azure, które są połączone z delegowanymi subskrypcjami i/lub grupami zasobów na platformie Azure
  - Zarządzanie połączonymi maszynami przy użyciu konstrukcji platformy Azure, takich Azure Policy i tagowanie
  - Upewnij się, że ten sam zestaw zasad jest stosowany w środowiskach hybrydowych klientów
  - Używanie Azure Security Center do monitorowania zgodności w środowiskach hybrydowych klientów
- Zarządzanie hybrydowymi klastrami Kubernetes na dużą skalę [— Azure Arc kubernetes (wersja zapoznawcza):](../../azure-arc/kubernetes/overview.md)
  - [Zarządzanie klastrami Kubernetes połączonymi z](../../azure-arc/kubernetes/quickstart-connect-cluster.md) delegowanymi subskrypcjami i/lub grupami zasobów na platformie Azure
  - [Używanie usługi GitOps](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) dla połączonych klastrów
  - Wymuszanie zasad w połączonych klastrach

[Azure Automation:](../../automation/index.yml)

- Używanie kont usługi Automation do uzyskiwania dostępu do zasobów delegowanych i pracy z nich

[Azure Backup:](../../backup/index.yml)

- Back up and restore customer data [from on-premises workloads, Azure VMs, Azure file shares, and more](../..//backup/backup-overview.md#what-can-i-back-up) (Kopii zapasowej i przywracanie danych klientów z obciążeń lokalnych, maszyn wirtualnych platformy Azure, udziałów plików platformy Azure i innych)
- Wyświetlanie danych dla wszystkich delegowanych zasobów klienta w [Centrum kopii zapasowych](../../backup/backup-center-overview.md)
- Skorzystaj z [Eksplorator usługi Backup,](../../backup/monitor-azure-backup-with-backup-explorer.md) aby wyświetlić informacje operacyjne dotyczące elementów kopii zapasowej (w tym zasobów platformy Azure, które nie zostały jeszcze skonfigurowane do tworzenia kopii zapasowych) i monitorowania informacji (zadań i alertów) dla delegowanych subskrypcji. Ta Eksplorator usługi Backup jest obecnie dostępna tylko dla danych maszyny wirtualnej platformy Azure.
- Używaj [Raporty usługi Backup](../../backup/configure-reports.md) delegowanych subskrypcji do śledzenia trendów historycznych, analizowania użycia magazynu kopii zapasowych oraz inspekcji kopii zapasowych i przywracania.

[Azure Blueprints:](../../governance/blueprints/index.yml)

- Użyj Azure Blueprints, aby aranżować wdrażanie szablonów zasobów i [](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) innych artefaktów (wymaga dodatkowego dostępu w celu przygotowania subskrypcji klienta)

[Azure Cost Management + Billing:](../../cost-management-billing/index.yml)

- W dzierżawie zarządzającej partnerzy programu CSP mogą wyświetlać i analizować koszty użycia przed opodatkowaniem (bez zakupów) dla klientów korzystających z planu platformy Azure oraz zarządzać nimi. Koszt będzie oparty na stawkach detalicznych i dostępie do kontroli dostępu na podstawie ról (RBAC) platformy Azure, które partner ma dla subskrypcji klienta. Obecnie można wyświetlać koszty użycia według stawek detalicznych dla każdej indywidualnej subskrypcji klienta na podstawie dostępu RBAC platformy Azure.

[Azure Key Vault:](../../key-vault/general/index.yml)

- Tworzenie magazynów kluczy w dzierżawach klientów
- Używanie tożsamości zarządzanej do tworzenia magazynów kluczy w dzierżawach klientów

[Azure Kubernetes Service (AKS):](../../aks/index.yml)

- Zarządzanie hostowanych środowisk Kubernetes oraz wdrażanie konteneryzowanych aplikacji w dzierżawach klientów i zarządzanie nimi
- Wdrażanie klastrów i zarządzanie nimi w dzierżawach klientów
-   Używanie Azure Monitor kontenerów do monitorowania wydajności w dzierżawach klientów

[Azure Migrate:](../../migrate/index.yml)

- Tworzenie projektów migracji w dzierżawie klienta i migrowanie maszyn wirtualnych

[Azure Monitor:](../../azure-monitor/index.yml)

- Wyświetlanie alertów dla delegowanych subskrypcji z możliwością wyświetlania i odświeżania alertów we wszystkich subskrypcjach
- Wyświetlanie szczegółów dziennika aktywności dla subskrypcji delegowanych
- [Analiza dzienników:](../../azure-monitor/logs/service-providers.md)wykonywanie zapytań o dane ze zdalnych obszarów roboczych w wielu dzierżawach (należy pamiętać, że konta automatyzacji używane do uzyskiwania dostępu do danych z obszarów roboczych w dzierżawach klientów muszą zostać utworzone w tej samej dzierżawie)
- [Tworzenie i wyświetlanie alertów dziennika aktywności w](../../azure-monitor/alerts/alerts-activity-log.md) dzierżawach klientów oraz zarządzanie nimi
- Tworzenie w dzierżawach klientów alertów, które wyzwalają automatyzację, takich jak Azure Automation runbook lub Azure Functions, w dzierżawie zarządzającej za pomocą webhook
- Tworzenie [ustawień diagnostycznych w](../..//azure-monitor/essentials/diagnostic-settings.md) dzierżawach klientów w celu wysyłania dzienników zasobów do obszarów roboczych w dzierżawie zarządzającej
- W przypadku obciążeń SAP [należy monitorować metryki rozwiązań SAP z zagregowanym widokiem w dzierżawach klientów](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Azure Networking:](../../networking/networking-overview.md)

- Wdrażanie usługi [Azure Virtual Network](../../virtual-network/index.yml) i wirtualnych kart sieciowych (vNICs) w dzierżawach zarządzanych i zarządzanie nimi
- Wdrażanie i [konfigurowanie Azure Firewall](../../firewall/overview.md) w celu ochrony zasobów Virtual Network klientów
- Zarządzanie usługami łączności, [takimi jak Azure Virtual WAN,](../../virtual-wan/virtual-wan-about.md) [ExpressRoute](../../expressroute/expressroute-introduction.md)i [bramy sieci VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- Użyj Azure Lighthouse do obsługi kluczowych scenariuszy dla [programu Azure Networking MSP](../../networking/networking-partners-msp.md)

[Azure Policy:](../../governance/policy/index.yml)

- Tworzenie i edytowanie definicji zasad w ramach subskrypcji delegowanych
- Wdrażanie definicji zasad i przypisań zasad w wielu dzierżawach
- Przypisywanie definicji zasad zdefiniowanych przez klienta w ramach subskrypcji delegowanych
- Klienci widzą zasady, które zostały przez dostawcę usług, wraz z zasadami, które zostały przez nich samodzielnie przez siebie samodzielnie zrównane
- Można [skorygować właściwości deployIfNotExists lub zmodyfikować przypisania w dzierżawie zarządzanej](../how-to/deploy-policy-remediation.md)
- Należy pamiętać, że wyświetlanie szczegółów zgodności dla niezgodnych zasobów w dzierżawach klientów nie jest obecnie obsługiwane

[Azure Resource Graph:](../../governance/resource-graph/index.yml)

- Teraz zawiera identyfikator dzierżawy w zwracanych wynikach zapytania, co pozwala określić, czy subskrypcja należy do dzierżawy zarządzanej

[Azure Security Center:](../../security-center/index.yml)

- Widoczność między dzierżawami
  - Monitorowanie zgodności z zasadami zabezpieczeń i zapewnianie pokrycia zabezpieczeń we wszystkich zasobach dzierżaw
  - Ciągłe monitorowanie zgodności z przepisami w wielu dzierżawach w jednym widoku
  - Monitorowanie, określanie priorytetów i rekomendacji dotyczących zabezpieczeń z akcjami przy użyciu obliczania wskaźnika bezpieczeństwa
- Zarządzanie poziomem zabezpieczeń między dzierżawami
  - Zarządzanie zasadami zabezpieczeń
  - Podjąć działania dotyczące zasobów, które są zgodne z zaleceniami zabezpieczeń z akcjami
  - Zbieranie i przechowywanie danych związanych z zabezpieczeniami
- Wykrywanie zagrożeń i ochrona między dzierżawami
  - Wykrywanie zagrożeń w zasobach dzierżaw
  - Stosowanie zaawansowanych środków kontroli ochrony przed zagrożeniami, takich jak dostęp just in time (JIT) do maszyny wirtualnej
  - Wzmacnianie konfiguracji sieciowej grupy zabezpieczeń za pomocą adaptacyjnego wzmacniania zabezpieczeń sieci
  - Upewnij się, że na serwerach są uruchomione tylko aplikacje i procesy, które powinny być z adaptacyjną kontrolą aplikacji
  - Monitorowanie zmian ważnych plików i wpisów rejestru za pomocą monitorowania integralności plików (FIM)
- Należy pamiętać, że cała subskrypcja musi być delegowana do dzierżawy zarządzającej; Azure Security Center scenariuszy nie są obsługiwane w przypadku delegowanych grup zasobów

[Azure Sentinel:](../../sentinel/multiple-tenants-service-providers.md)

- Zarządzanie Azure Sentinel zasobów [w dzierżawach klientów](../../sentinel/multiple-tenants-service-providers.md)
- [Śledzenie ataków i wyświetlanie alertów zabezpieczeń w wielu dzierżawach](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Wyświetlanie zdarzeń w wielu](../../sentinel/multiple-workspace-view.md) obszarach Azure Sentinel w różnych dzierżawach

[Azure Service Health:](../../service-health/index.yml)

- Monitorowanie kondycji zasobów klienta za pomocą Azure Resource Health
- Śledzenie kondycji usług platformy Azure używanych przez klientów

[Azure Site Recovery:](../../site-recovery/index.yml)

- Zarządzanie opcjami odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w dzierżawach klientów (pamiętaj, że nie można używać kont `RunAs` do kopiowania rozszerzeń maszyn wirtualnych)

[Azure Virtual Machines:](../../virtual-machines/index.yml)

- Korzystanie z rozszerzeń maszyny wirtualnej w celu zapewnienia konfiguracji po wdrożeniu i zadań automatyzacji na maszynach wirtualnych platformy Azure
- Rozwiązywanie problemów z maszynami wirtualnych platformy Azure przy użyciu diagnostyki rozruchu
- Uzyskiwanie dostępu do maszyn wirtualnych za pomocą konsoli szeregowej
- Integrowanie maszyn wirtualnych z usługą Azure Key Vault hasłami, wpisami tajnymi lub kluczami kryptograficznymi w celu szyfrowania dysków przy użyciu tożsamości zarządzanej za pośrednictwem [zasad,](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)zapewniając, że wpisy tajne są przechowywane w Key Vault dzierżawach zarządzanych
- Należy pamiętać, że nie można używać Azure Active Directory do zdalnego logowania do maszyn wirtualnych

Żądania pomocy technicznej:

- [Otwórz żądania pomocy technicznej **z pomocy i**](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) obsługi technicznej w Azure Portal zasobów delegowanych (wybierając plan pomocy technicznej dostępny w zakresie delegowanym)
- Wyświetlanie [limitów przydziału](/rest/api/reserved-vm-instances/quotaapi) usług platformy Azure dla delegowanych zasobów klientów i zarządzanie nimi za pomocą interfejsu API limitów przydziału platformy Azure

## <a name="current-limitations"></a>Bieżące ograniczenia

We wszystkich scenariuszach należy pamiętać o następujących bieżących ograniczeniach:

- Żądania obsługiwane przez Azure Resource Manager mogą być wykonywane przy użyciu Azure Lighthouse. Wartości URI operacji dla tych żądań rozpoczynają się od `https://management.azure.com` . Jednak żądania obsługiwane przez wystąpienie typu zasobu (na przykład dostęp do wpisów tajnych usługi Key Vault lub dostęp do danych magazynu) nie są obsługiwane przez Azure Lighthouse. Identyfikatory URI operacji dla tych żądań zwykle zaczynają się od adresu unikatowego dla wystąpienia, takiego `https://myaccount.blob.core.windows.net` jak lub `https://mykeyvault.vault.azure.net/` . Te drugie są również zazwyczaj operacjami na danych, a nie operacjami zarządzania.
- Przypisania ról muszą używać [wbudowanych ról platformy Azure.](../../role-based-access-control/built-in-roles.md) Wszystkie role wbudowane są obecnie obsługiwane za pomocą zarządzania zasobami delegowanymi platformy Azure z wyjątkiem ról właściciela lub wbudowanych ról z [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) uprawnieniami. Rola administratora dostępu użytkowników jest obsługiwana tylko w przypadku ograniczonego użycia podczas [przypisywania ról do tożsamości zarządzanych.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)  Role niestandardowe i [role klasycznego administratora subskrypcji](../../role-based-access-control/classic-administrators.md) nie są obsługiwane.
- Chociaż można dołączać subskrypcje, które używają usługi Azure Databricks, użytkownicy w dzierżawie zarządzającej nie mogą obecnie uruchamiać Azure Databricks roboczych w ramach subskrypcji delegowane.
- Można dołączać subskrypcje i grupy zasobów z blokadami zasobów, ale nie uniemożliwiają one wykonania akcji przez użytkowników w dzierżawie zarządzającej. Przypisania [odmowy,](../../role-based-access-control/deny-assignments.md) które chronią zasoby zarządzane przez system, takie jak utworzone przez aplikacje zarządzane platformy Azure lub Azure Blueprints (przypisania odmowy przypisane przez system), uniemożliwiają użytkownikom w dzierżawie zarządzającej działanie na tych zasobach; Jednak w tej chwili użytkownicy w dzierżawie klienta nie mogą tworzyć własnych przypisań odmowy (przypisań odmowy przypisanych przez użytkownika).
- Delegowanie subskrypcji w chmurze [krajowej](../../active-directory/develop/authentication-national-cloud.md) i chmurze publicznej platformy Azure lub w dwóch oddzielnych chmurach krajowych nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

- Dołączanie klientów do usługi Azure Lighthouse przy użyciu szablonów usługi [Azure Resource Manager](../how-to/onboard-customer.md) lub poprzez opublikowanie oferty prywatnych lub publicznych usług zarządzanych w [Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Wyświetl klientów i zarządzaj nimi,](../how-to/view-manage-customers.md) przechodząc do strony **Moi klienci** w Azure Portal.
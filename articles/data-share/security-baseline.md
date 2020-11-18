---
title: Podstawa zabezpieczeń Azure dla udziału danych platformy Azure
description: Linia bazowa zabezpieczeń udziału danych platformy Azure zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń Azure.
author: msmbaldwin
ms.service: data-share
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7bf78194b50e2e2335d807504ebca5f8a0e9f0eb
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849214"
---
# <a name="azure-security-baseline-for-azure-data-share"></a>Podstawa zabezpieczeń Azure dla udziału danych platformy Azure

Ta linia bazowa zabezpieczeń stosuje wskazówki z [wersji 1,0 usługi Azure Security test](../security/benchmarks/overview-v1.md) w celu Microsoft Azure udziału danych. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące udziału danych platformy Azure. **Formanty** nie mają zastosowania do udziału danych platformy Azure zostały wykluczone.

 
Aby dowiedzieć się, jak usługa Azure Data Share jest całkowicie mapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pełny plik mapowania zabezpieczeń usługi Azure Data Share](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników związanych z udziałem danych platformy Azure za pośrednictwem Azure monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Alternatywnie możesz włączyć i dołączyć te dane do usługi Azure wskaźnikowej lub SIEM innej firmy.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md) 

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innej firmy](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: dzienniki aktywności, które są automatycznie dostępne, zawierają wszystkie operacje zapisu (Put, post, Delete) dla zasobów udziałów danych platformy Azure z wyjątkiem operacji odczytu (Get). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób.

Włącz dzienniki diagnostyczne dla udziału danych platformy Azure, a w odniesieniu do dzienników diagnostycznych MicrosoftDataShareSentShareSnapshotsLog &amp; MicrosoftDataShareReceivedShareSnapshotsLog. Te dzienniki umożliwiają przechwycenie informacji o kluczach, takich jak godzina rozpoczęcia synchronizacji, czas zakończenia, stan i inne szczegóły. Te dzienniki mogą być niezbędne do późniejszego badania zdarzeń związanych z bezpieczeństwem i wykonywania ćwiczeń śledczej.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Jak skonfigurować ustawienia diagnostyczne dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: Upewnij się, że wszystkie konta magazynu lub log Analytics obszary robocze używane do przechowywania dzienników udziałów danych platformy Azure mają ustawiony okres przechowywania dziennika zgodnie z regulacjami zgodności w organizacji.

- [Jak skonfigurować okres przechowywania obszaru roboczego Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

- [Przechowywanie dzienników zasobów na koncie usługi Azure Storage](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników związanych z zasobami udziałów danych platformy Azure w celu nietypowego zachowania i regularnego przeglądania wyników. Użyj Azure Monitor i Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innej firmy.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md) 

- [Wprowadzenie do Log Analytics zapytań](/azure/azure-monitor/log-query/get-started-portal) 

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](../azure-monitor/log-query/get-started-queries.md) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Użyj Azure Security Center z obszarem roboczym log Analytics do monitorowania i generowania alertów dotyczących nietypowego działania znalezionego w dziennikach i zdarzeniach zabezpieczeń. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md) 

- [Jak zarządzać alertami w Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md) 

- [Jak ostrzec dane dziennika usługi log Analytics](../azure-monitor/learn/tutorial-response.md) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: tożsamość i kontrola dostępu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: usługa Azure Data Share obsługuje uwierzytelnianie SSO przy użyciu Azure Active Directory. Zmniejsz liczbę tożsamości i poświadczeń, które użytkownicy muszą zarządzać, włączając Logowanie jednokrotne w usłudze przy użyciu istniejących tożsamości w organizacji.

- [Opis logowania jednokrotnego w usłudze Azure AD](/azure/active-directory/manage-apps/what-is-single-sign-o)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włącz usługę Azure AD MFA i postępuj zgodnie z zaleceniami Azure Security Center i zaleceń dotyczących dostępu.

- [Jak włączyć usługę MFA na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md) 

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj bezpiecznej, zarządzanej przez platformę Azure stacji roboczej (znanej także jako stacja robocza dostępu uprzywilejowanego lub dostępem uprzywilejowanym) dla zadań administracyjnych, które wymagają podwyższonego poziomu uprawnień.

- [Informacje na temat bezpiecznych stacji roboczych zarządzanych przez platformę Azure](../active-directory/devices/concept-azure-managed-workstation.md)
 

- [Jak włączyć usługę Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Udział danych platformy Azure współpracuje z ogólnymi rolami platformy Azure wbudowanymi ](../role-based-access-control/built-in-roles.md#general)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: usługa Azure AD udostępnia dzienniki, które ułatwiają odnajdywanie starych kont. Ponadto za pomocą przeglądów tożsamości i dostępu w usłudze Azure AD można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników może być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring/) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Udział danych platformy Azure współpracuje z ogólnymi rolami platformy Azure wbudowanymi ](../role-based-access-control/built-in-roles.md#general)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: masz dostęp do źródeł zdarzeń związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem związanym z dziennikami, które umożliwiają integrację z dowolnym narzędziem Siem/monitoring.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: użyj funkcji Azure AD Identity Protection, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami użytkowników. Możesz również pozyskać dane do usługi Azure wskaźnikowej na potrzeby dalszej analizy.

- [Jak wyświetlić ryzykowne logowania usługi Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Azure Security test: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Korzystaj z usługi Azure RBAC, aby zarządzać dostępem do zasobów

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby zarządzać dostępem do danych i zasobów związanych z zasobami udziałów danych platformy Azure. w przeciwnym razie użyj metod kontroli dostępu specyficznych dla usługi.

- [Jak skonfigurować RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md) 

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty Azure monitor dla momentu wprowadzenia zmian do krytycznych zasobów platformy Azure.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/alerts-activity-log.md) 

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: udział danych platformy Azure nie zezwala na instalację narzędzi do skanowania w poszukiwaniu luk w zabezpieczeniach.

Ogólnie rzecz biorąc, postępuj zgodnie z zaleceniami Azure Security Center, aby przeprowadzać oceny luk w zabezpieczeniach na maszynach wirtualnych platformy Azure, na obrazach kontenerów i serwerach SQL.

Użyj rozwiązania innych firm do przeprowadzania ocen luk w zabezpieczeniach na urządzeniach sieciowych i aplikacjach sieci Web. Podczas przeprowadzania skanowania zdalnego nie należy używać pojedynczego, bezterminowego konta administratora. Rozważ zaimplementowanie metody inicjowania obsługi JIT dla konta skanowania. Poświadczenia dla konta skanowania powinny być chronione, monitorowane i używane tylko w celu skanowania w poszukiwaniu luk w zabezpieczeniach.

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Zastosuj znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: Zastosuj znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji w miarę potrzeb w celu organizowania i śledzenia zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](/azure/billing/billing-create-subscription) 

- [Jak utworzyć Grupy zarządzania](/azure/governance/management-groups/create) 

- [Tworzenie i używanie tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania do zasobów obliczeniowych zgodnie z potrzebami organizacji.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji.
Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania zasobów w ramach ich subskrypcji. Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md) 

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: usuwanie zasobów platformy Azure, gdy nie są już potrzebne, można to zrobić za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia.

- [Usuwanie zasobów i grup zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)

Udział danych platformy Azure nie uwidacznia systemu operacyjnego ani nie pozwala na instalowanie aplikacji innych firm w swoich zasobach.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby ograniczyć usługi, które możesz udostępnić w danym środowisku.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod, taki jak niestandardowe definicje Azure Policy, szablony Azure Resource Manager i skrypty konfiguracji żądanego stanu, oraz zarządzać nimi. Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps lub Active Directory w przypadku integracji z programem TFS.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Informacje o uprawnieniach i grupach na platformie Azure DevOps](/azure/devops/organizations/security/about-permissions) 

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "datashare", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

- [Jak skonfigurować Azure Policy i zarządzać nimi](../governance/policy/tutorials/create-and-manage.md) 

- [Jak używać aliasów](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: używanie tożsamości zarządzanych do świadczenia usług platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure AD. Zarządzane tożsamości umożliwiają uwierzytelnianie w dowolnej usłudze obsługującej uwierzytelnianie w usłudze Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak skonfigurować tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)

---
title: Środowiska zarządzania wieloma dzierżawami
description: Zarządzanie zasobami delegowanymi platformy Azure umożliwia środowisko zarządzania między dzierżawami.
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 66e2b2f7fbbae5940b576ead22a416625af3c058
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418522"
---
# <a name="cross-tenant-management-experiences"></a>Środowiska zarządzania wieloma dzierżawami

Jako dostawca usług możesz używać [zarządzania zasobami delegowanymi platformy Azure](../concepts/azure-delegated-resource-management.md) do zarządzania zasobami platformy Azure dla wielu klientów z poziomu własnej dzierżawy w [witrynie Azure portal.](https://portal.azure.com) Większość zadań i usług można wykonywać na delegowanych zasobów platformy Azure w dzierżawach zarządzanych. W tym artykule opisano niektóre z rozszerzonych scenariuszy, w których zarządzanie zasobami delegowanymi platformy Azure może być skuteczne.

> [!NOTE]
> Zarządzanie zasobami delegowanymi platformy Azure może być również używane [w przedsiębiorstwie, które ma wielu dzierżaw usługi Azure AD,](enterprise.md) aby uprościć administrowanie między dzierżawcami.

## <a name="understanding-customer-tenants"></a>Opis najemców klientów

Dzierżawa usługi Azure Active Directory (Azure AD) jest reprezentacją organizacji. Jest to dedykowane wystąpienie usługi Azure AD, które organizacja otrzymuje podczas tworzenia relacji z firmą Microsoft, rejestrując się na platformie Azure, usłudze Microsoft 365 lub innych usługach. Każda dzierżawa usługi Azure AD jest odrębna i oddzielona od innych dzierżaw usługi Azure AD i ma własny identyfikator dzierżawy (identyfikator GUID). Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Zazwyczaj w celu zarządzania zasobami platformy Azure dla klienta dostawcy usług będą musieli zalogować się do witryny Azure portal przy użyciu konta skojarzonego z dzierżawą tego klienta, wymagając od administratora w dzierżawie klienta tworzenia kont użytkowników dla dostawcy usług i zarządzania nimi.

W przypadku zarządzania zasobami delegowanymi platformy Azure proces dołączania określa użytkowników w dzierżawie dostawcy usług, którzy będą mogli uzyskiwać dostęp do subskrypcji, grup zasobów i zasobów w dzierżawie klienta oraz zarządzać nimi. Ci użytkownicy mogą następnie zalogować się do witryny Azure portal przy użyciu własnych poświadczeń. W witrynie Azure portal mogą zarządzać zasobami należącymi do wszystkich klientów, do których mają dostęp. Można to zrobić, odwiedzając [stronę Moi klienci](../how-to/view-manage-customers.md) w witrynie Azure portal lub pracując bezpośrednio w kontekście subskrypcji tego klienta, w witrynie Azure portal lub za pośrednictwem interfejsów API.

Zarządzanie zasobami delegowanymi platformy Azure umożliwia większą elastyczność zarządzania zasobami dla wielu klientów bez konieczności logowania się do różnych kont w różnych dzierżawach. Na przykład dostawca usług może mieć dwóch klientów o różnych obowiązkach i poziomach dostępu. Korzystając z zarządzania zasobami delegowanymi platformy Azure, autoryzowani użytkownicy mogą logować się do dzierżawy dostawcy usług, aby uzyskać dostęp do tych zasobów.

![Zasoby klienta zarządzane za pośrednictwem jednego dzierżawcy dostawcy usług](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Interfejsy API i obsługa narzędzi do zarządzania

Zadania zarządzania można wykonywać na delegowanych zasobów bezpośrednio w portalu lub przy użyciu interfejsów API i narzędzi zarządzania (takich jak interfejs wiersza polecenia platformy Azure i azure powershell). Wszystkie istniejące interfejsy API mogą być używane podczas pracy z delegowanymi zasobami, o ile funkcja jest obsługiwana do zarządzania między dzierżawami, a użytkownik ma odpowiednie uprawnienia.

[Polecenie cmdlet get-azsubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) platformy Azure powershell pokazuje **identyfikator dzierżawy** dla każdej subskrypcji, umożliwiając określenie, czy zwrócona subskrypcja należy do dzierżawy dostawcy usług, czy do dzierżawy klienta zarządzanego.

Podobnie polecenia interfejsu wiersza polecenia platformy Azure, takie jak [lista kont az,](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) pokazują atrybuty **homeTenantId** i **managedByTenants.**

> [!TIP]
> Jeśli te wartości nie są widoczne podczas korzystania z interfejsu `az account clear` wiersza `az login --identity`polecenia platformy Azure, spróbuj wyczyścić pamięć podręczną, uruchamiając go, a następnie .

Udostępniamy również interfejsy API, które są specyficzne dla wykonywania zadań zarządzania zasobami delegowanymi platformy Azure. Aby uzyskać więcej informacji, zobacz sekcję **Odwołanie.**

## <a name="enhanced-services-and-scenarios"></a>Ulepszone usługi i scenariusze

Większość zadań i usług można wykonywać na delegowanych zasobów w zarządzanych dzierżaw. Poniżej znajdują się niektóre z kluczowych scenariuszy, w których zarządzanie między dzierżawami może być skuteczne.

[Usługa Azure Arc dla serwerów (wersja zapoznawcza)](../../azure-arc/servers/overview.md):

- [Łączenie komputerów z systemem Windows Server lub Linux poza platformą Azure](../../azure-arc/servers/quickstart-onboard-portal.md) z delegowanymi subskrypcjami i/lub grupami zasobów na platformie Azure
- Zarządzanie połączonymi komputerami przy użyciu konstrukcji platformy Azure, takich jak zasady platformy Azure i tagowanie

[Automatyzacja platformy Azure](../../automation/index.yml):

- Korzystanie z kont automatyzacji w celu uzyskiwania dostępu do delegowanych zasobów klientów i pracy z nich

[Kopia zapasowa platformy Azure:](../../backup/index.yml)

- Twoji i przywracanie danych klientów w dzierżawach klientów
- Użyj [Eksploratora kopii zapasowych,](../../backup/monitor-azure-backup-with-backup-explorer.md) aby wyświetlić informacje operacyjne dotyczące elementów kopii zapasowej (w tym zasobów platformy Azure, które nie zostały jeszcze skonfigurowane do tworzenia kopii zapasowych) i informacji o monitorowaniu (zadań i alertów) dla delegowanych subskrypcji. Eksplorator kopii zapasowych jest obecnie dostępny tylko dla danych maszyny Wirtualnej platformy Azure.
- Raporty [kopii zapasowych](../../backup/configure-reports.md) w ramach delegowanych subskrypcji umożliwia śledzenie trendów historycznych, analizowanie zużycia magazynu kopii zapasowych oraz inspekcja kopii zapasowych i przywracania.

[Usługa Azure Kubernetes (AKS)](../../aks/index.yml):

- Zarządzanie hostowanymi środowiskami kubernetes oraz wdrażanie i zarządzanie konteneryzowanymi aplikacjami w ramach dzierżawców klientów

[Monitor platformy Azure](../../azure-monitor/index.yml):

- Wyświetlanie alertów dotyczących subskrypcji delegowanych z możliwością wyświetlania alertów we wszystkich subskrypcjach
- Wyświetlanie szczegółów dziennika aktywności dla subskrypcji delegowanych
- Analiza dzienników: zapytanie o dane z zdalnych obszarów roboczych klientów w wielu dzierżawach
- Tworzenie alertów w dzierżawach klientów wyzwalających automatyzację, takich jak elementy runbook usługi Azure Automation lub usługi Azure Functions, w dzierżawie dostawcy usług za pośrednictwem łączy webhook

[Zasady platformy Azure:](../../governance/policy/index.yml)

- Migawki zgodności pokazują szczegółowe informacje dotyczące przypisanych zasad w ramach delegowanych subskrypcji
- Tworzenie i edytowanie definicji zasad w ramach subskrypcji delegowanej
- Przypisywanie zdefiniowanych przez klienta definicji zasad w ramach subskrypcji delegowanej
- Klienci widzą zasady autorstwa usługodawcy wraz z zasadami, które sami stworzyli
- Można [korygować deployIfNotExists lub modyfikować przypisania w dzierżawie klienta](../how-to/deploy-policy-remediation.md)

[Wykres zasobów platformy Azure:](../../governance/resource-graph/index.yml)

- Teraz zawiera identyfikator dzierżawy w zwróconych wynikach kwerendy, co pozwala określić, czy subskrypcja należy do dzierżawy klienta lub dzierżawy dostawcy usług

[Usługa Azure Security Center:](../../security-center/index.yml)

- Widoczność między dzierżawcami
  - Monitorowanie zgodności z zasadami zabezpieczeń i zapewnienie ochrony wszystkich zasobów najemców
  - Ciągłe monitorowanie zgodności z przepisami przez wielu klientów w jednym widoku
  - Monitorowanie, klasyfikowanie i ustalanie priorytetów zaleceń dotyczących zabezpieczeń z 2015 r. z zabezpieczonych
- Zarządzanie bezpieczeństwem między dzierżawcami
  - Zarządzanie zasadami zabezpieczeń
  - Podejmij działania w odniesieniu do zasobów niezgodnych z zaleceniami dotyczącymi zabezpieczeń, które można zasuwają
  - Zbieranie i przechowywanie danych związanych z zabezpieczeniami
- Wykrywanie i ochrona zagrożeń między dzierżawcami
  - Wykrywanie zagrożeń w zasobach dzierżawców
  - Stosowanie zaawansowanych kontroli ochrony przed zagrożeniami, takich jak dostęp maszyny wirtualnej just-in-time (JIT)
  - Utwardzanie konfiguracji sieciowej grupy zabezpieczeń dzięki adaptacyjnej hartowaniu sieci
  - Upewnij się, że serwery działają tylko w aplikacjach i procesach, które powinny być wyposażone w adaptacyjne elementy sterujące aplikacjami
  - Monitorowanie zmian ważnych plików i wpisów rejestru za pomocą monitorowania integralności plików (FIM)

[Wartownik azure:](../../sentinel/multiple-tenants-service-providers.md)

- Zarządzanie zasobami usługi Azure Sentinel [w dzierżawach klientów](../../sentinel/multiple-tenants-service-providers.md)
- [Śledzenie ataków i wyświetlanie alertów zabezpieczeń w wielu dzierżawach klientów](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Kondycja usługi Azure:](../../service-health/index.yml)

- Monitorowanie kondycji zasobów klientów za pomocą usługi Azure Resource Health
- Śledzenie kondycji usług platformy Azure używanych przez klientów

[Odzyskiwanie witryny platformy Azure:](../../site-recovery/index.yml)

- Zarządzanie opcjami odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w dzierżawcach klientów (należy pamiętać, że nie można kopiować rozszerzeń maszyn wirtualnych za pomocą kont RunAs)

[Maszyny wirtualne platformy Azure:](../../virtual-machines/index.yml)

- Użyj rozszerzeń maszyn wirtualnych, aby zapewnić konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure w dzierżawach klientów
- Używanie diagnostyki rozruchu do rozwiązywania problemów z maszynami wirtualnymi platformy Azure w dzierżawach klientów
- Dostęp do maszyn wirtualnych z konsolą szeregową w dzierżawach klientów
- Należy pamiętać, że nie można używać usługi Azure Active Directory do zdalnego logowania do maszyny Wirtualnej i nie można zintegrować maszyny Wirtualnej z magazynem kluczy dla haseł, wpisów tajnych lub kluczy kryptograficznych do szyfrowania dysku

[Wirtualna sieć platformy Azure:](../../virtual-network/index.yml)

- Wdrażanie sieci wirtualnych i kart vNIC oraz zarządzanie nimi w ramach dzierżawców klientów

Prośby o pomoc techniczną:

- Otwórz żądania pomocy technicznej dla delegowanych zasobów z **bloku Pomocy + pomocy technicznej** w witrynie Azure portal (wybierając plan pomocy technicznej dostępny dla delegowanego zakresu)

## <a name="current-limitations"></a>Bieżące ograniczenia
We wszystkich scenariuszach należy pamiętać o następujących bieżących ograniczeniach:

- Żądania obsługiwane przez usługę Azure Resource Manager można wykonywać przy użyciu zarządzania zasobami delegowanymi platformy Azure. Identyfikatory identyfikatorów URI operacji `https://management.azure.com`dla tych żądań zaczynają się od . Jednak żądania, które są obsługiwane przez wystąpienie typu zasobu (takich jak keyvault wpisy tajne dostępu lub dostępu do danych magazynu) nie są obsługiwane przez zarządzanie zasobami delegowanych platformy Azure. Identyfikatory identyfikatorów URI operacji dla tych żądań zazwyczaj zaczynają się `https://myaccount.blob.core.windows.net` `https://mykeyvault.vault.azure.net/`od adresu, który jest unikatowy dla wystąpienia, takich jak lub . Te ostatnie są również zazwyczaj operacji danych, a nie operacji zarządzania. 
- Przypisania ról muszą używać [wbudowanych ról](../../role-based-access-control/built-in-roles.md)kontroli dostępu opartej na rolach (RBAC). Wszystkie wbudowane role są obecnie obsługiwane za pomocą usługi Azure zarządzanie zasobami delegowanymi, z wyjątkiem właściciela lub dowolnych wbudowanych ról z [uprawnieniami DataActions.](../../role-based-access-control/role-definitions.md#dataactions) Rola Administratora dostępu użytkownika jest obsługiwana tylko w przypadku ograniczonego wykorzystania [ról do tożsamości zarządzanych](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Role niestandardowe i [klasyczne role administratora subskrypcji](../../role-based-access-control/classic-administrators.md) nie są obsługiwane.
- Chociaż można dołączać subskrypcje, które używają usługi Azure Databricks, użytkownicy w dzierżawie zarządzania nie można uruchomić obszarów roboczych usługi Azure Databricks w ramach subskrypcji delegowanej w tej chwili.
- Chociaż można dołączać subskrypcje i grupy zasobów dla zarządzania zasobami delegowanymi platformy Azure, które mają blokady zasobów, te blokady nie uniemożliwią wykonywania akcji przez użytkowników w dzierżawie zarządzającej. [Odmów przydziałów,](../../role-based-access-control/deny-assignments.md) które chronią zasoby zarządzane przez system, takie jak te utworzone przez aplikacje zarządzane platformy Azure lub plany platformy Azure (przypisane do systemu przypisane przypisane przypisane przypisane przypisane przypisane przypisane przypisane przypisane przypisane przypisane przypisane do nich przypisane przypisane przypisane przypisane do nich przypisane przydziały, uniemożliwiają użytkownikom w dzierżawie zarządzającej działanie na tych zasobach; jednak w tej chwili użytkownicy w dzierżawie klienta nie mogą tworzyć własnych przypisań odmowy (przypisanych przez użytkownika przypisań odmowy).
- Użytkownicy w dzierżawie zarządzającej nie będą mieli dostępu do wyświetlania informacji rozliczeniowych dla delegowanej subskrypcji klienta, nawet jeśli mają wbudowaną rolę, która zazwyczaj zezwala na dostęp. Dzieje się tak, ponieważ dostęp do informacji rozliczeniowych wymaga dodatkowych kroków, które są obecnie obsługiwane tylko dla użytkowników w ramach tej samej dzierżawy.

## <a name="next-steps"></a>Następne kroki

- Dołączanie klientów do zarządzania zasobami delegowanymi platformy Azure za [pomocą szablonów usługi Azure Resource Manager](../how-to/onboard-customer.md) lub przez [opublikowanie prywatnej lub publicznej oferty usług zarządzanych w portalu Azure Marketplace.](../how-to/publish-managed-services-offers.md)
- [Wyświetlanie klientów i zarządzanie nimi,](../how-to/view-manage-customers.md) przechodząc do **witryny Moi klienci** w witrynie Azure portal.

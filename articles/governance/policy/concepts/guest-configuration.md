---
title: Dowiedz się, jak przeprowadzać inspekcje zawartości maszyn wirtualnych
description: Dowiedz się, jak usługa Azure Policy używa agenta konfiguracji gościa do inspekcji ustawień wewnątrz maszyn wirtualnych.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: e4899f6b3108cabb4e9cdd36e4b2bc5cd2f1cbd4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538039"
---
# <a name="understand-azure-policys-guest-configuration"></a>Opis konfiguracji gościa zasad platformy Azure

Oprócz inspekcji i [korygowania](../how-to/remediate-resources.md) zasobów platformy Azure usługa Azure Policy może przeprowadzać inspekcję ustawień wewnątrz komputera. Taka weryfikacja jest wykonywana przez klienta i rozszerzenie konfiguracji gościa. Rozszerzenie to, obsługiwane za pośrednictwem klienta, umożliwia weryfikację następujących ustawień:

- Konfiguracja systemu operacyjnego
- Konfiguracja lub obecność aplikacji
- Ustawienia środowiska

W tej chwili większość zasad konfiguracji gościa zasad usługi Azure Policy tylko inspekcji ustawień wewnątrz komputera. Nie stosują konfiguracji. Wyjątkiem jest jedna wbudowana [zasada, do którą odwołuje się poniżej](#applying-configurations-using-guest-configuration).

## <a name="extension-and-client"></a>Rozszerzenie i klient

Aby przeprowadzić inspekcję ustawień wewnątrz maszyny, jest włączone [rozszerzenie maszyny wirtualnej.](../../../virtual-machines/extensions/overview.md) Rozszerzenie pobiera odpowiednie przypisanie zasad i odpowiednią definicję konfiguracji.

### <a name="limits-set-on-the-extension"></a>Limity ustawione na rozszerzeniu

Aby ograniczyć rozszerzenie od wpływu aplikacji działających wewnątrz komputera, konfiguracja gościa nie może przekraczać więcej niż 5% procesora CPU. To ograniczenie istnieje zarówno dla wbudowanych i niestandardowych definicji.

## <a name="register-guest-configuration-resource-provider"></a>Zarejestruj dostawcę zasobów konfiguracji gościa

Aby można było używać konfiguracji gościa, należy zarejestrować dostawcę zasobów. Można zarejestrować się za pośrednictwem [portalu](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)lub [Interfejsu wiersza polecenia platformy Azure.](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) Dostawca zasobów jest rejestrowany automatycznie, jeśli przypisanie zasad konfiguracji gościa odbywa się za pośrednictwem portalu.

## <a name="validation-tools"></a>Narzędzia sprawdzania poprawności

Wewnątrz komputera klient konfiguracji gościa używa narzędzi lokalnych do uruchomienia inspekcji.

W poniższej tabeli przedstawiono listę narzędzi lokalnych używanych w każdym obsługiwanym systemie operacyjnym:

|System operacyjny|Narzędzie sprawdzania poprawności|Uwagi|
|-|-|-|
|Windows|[Konfiguracja żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview) w wersji 2| |
|Linux|[Szef kuchni InSpec](https://www.chef.io/inspec/)| Jeśli Ruby i Python nie są na komputerze, są one instalowane przez rozszerzenie konfiguracji gościa. |

### <a name="validation-frequency"></a>Częstotliwość sprawdzania poprawności

Klient konfiguracji gościa sprawdza dostępność nowej zawartości co 5 minut. Po odebraniu przypisania gościa ustawienia tej konfiguracji są ponownie sprawdzane w odstępie 15 minut.
Wyniki są wysyłane do dostawcy zasobów konfiguracji gościa po zakończeniu inspekcji. Po wystąpieniu [wyzwalacza oceny](../how-to/get-compliance-data.md#evaluation-triggers) zasad stan komputera jest zapisywany do dostawcy zasobów konfiguracji gościa. Ta aktualizacja powoduje, że zasady platformy Azure do oceny właściwości usługi Azure Resource Manager. Ocena zasad platformy Azure na żądanie pobiera najnowszą wartość od dostawcy zasobów konfiguracji gościa. Jednak nie wyzwala nowy audyt konfiguracji w komputerze.

## <a name="supported-client-types"></a>Obsługiwane typy klientów

W poniższej tabeli przedstawiono listę obsługiwanych systemów operacyjnych na obrazach platformy Azure:

|Wydawca|Nazwa|Wersje|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ ( Credativ )|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Klient systemu Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5, 7.6, 7.7|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6, 7.7, 7.8|
|Suse|SLES|12 SP3|

### <a name="unsupported-client-types"></a>Nieobsługiwały typy klientów

System Windows Server Nano Server nie jest obsługiwany w żadnej wersji.

## <a name="guest-configuration-extension-network-requirements"></a>Wymagania sieciowe rozszerzenia konfiguracji gościa

Aby komunikować się z dostawcą zasobów konfiguracji gościa na platformie Azure, maszyny wymagają dostępu wychodzącego do centrów danych platformy Azure na porcie **443**. Jeśli sieć na platformie Azure nie zezwala na ruch wychodzący, skonfiguruj wyjątki za pomocą reguł [sieciowej grupy zabezpieczeń.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
[Tag usługi](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" może służyć do odwoływania się do usługi konfiguracji gościa.

## <a name="azure-managed-identity-requirements"></a>Wymagania dotyczące tożsamości zarządzanej platformy Azure

**DeployIfNotExists** zasady, które dodają rozszerzenie do maszyn wirtualnych również włączyć system przypisany tożsamości zarządzanej, jeśli nie istnieje.

> [!WARNING]
> Należy unikać włączania tożsamości zarządzanej przypisanej przez użytkownika do maszyn wirtualnych w zakresie zasad, które umożliwiają tożsamości zarządzanej przypisanej przez system. Przypisana przez użytkownika tożsamość zostanie zastąpiona i może przestać odpowiadać.

## <a name="guest-configuration-definition-requirements"></a>Wymagania dotyczące definicji konfiguracji gościa

Każdy inspekcji prowadzony przez konfigurację gościa wymaga dwóch definicji zasad, **DeployIfNotExists** definicji i **AuditIfNotExists** definicji. 

Definicja zasad **DeployIfNotExists** sprawdza poprawność i poprawia następujące elementy:

- Sprawdź poprawność maszyna została przypisana konfiguracja do oceny. Jeśli obecnie nie ma przypisania, pobierz przypisanie i przygotuj urządzenie przez:
  - Uwierzytelnianie na komputerze przy użyciu [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalowanie najnowszej wersji rozszerzenia **Microsoft.GuestConfiguration**
  - Instalowanie narzędzi i zależności [sprawdzania poprawności,](#validation-tools) jeśli to konieczne

Jeśli **deployIfNotExists** przypisanie jest niezgodne, można użyć [zadania korygowania.](../how-to/remediate-resources.md#create-a-remediation-task)

Gdy **deployIfNotExist** przypisanie jest zgodne, **AuditIfNotExists** przypisania zasad określa, czy przypisanie gościa jest zgodne lub niezgodne. Narzędzie sprawdzania poprawności udostępnia wyniki klientowi konfiguracji gościa. Klient przekazuje wyniki do rozszerzenia gościa, który udostępnia je za pośrednictwem dostawcy zasobów konfiguracji gościa.

Usługa Azure Policy używa właściwości **compliance statusus** dostawców zasobów konfiguracji gościa do zgłaszania zgodności w węźle **Zgodność.** Aby uzyskać więcej informacji, zobacz [uzyskiwanie danych dotyczących zgodności](../how-to/get-compliance-data.md).

> [!NOTE]
> **DeployIfNotExists** zasady jest wymagane dla **AuditIfNotExists** zasady do zwracania wyników. Bez **DeployIfNotExists,** **AuditIfNotExists** zasady pokazuje "0 z 0" zasobów jako stan.

Wszystkie wbudowane zasady konfiguracji gościa są uwzględnione w inicjatywie grupowania definicji do użycia w przydziałach. Wbudowana inicjatywa o nazwie _ \[Preview\]: Audit Password security inside Linux and Windows machines_ contains 18 policies. Istnieje sześć **DeployIfNotExists** i **AuditIfNotExists** pary dla systemu Windows i trzy pary dla systemu Linux. Logika [definicji zasad](definition-structure.md#policy-rule) sprawdza, czy tylko docelowy system operacyjny jest oceniany.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Inspekcja ustawień systemu operacyjnego zgodnie z branżowymi punktami bazowymi

Jedna inicjatywa w usłudze Azure Policy umożliwia inspekcję ustawień systemu operacyjnego poniżej "linii bazowej". _ \[Definicja,\]Wersja zapoznawcza: Inspekcja maszyn wirtualnych systemu Windows, które nie są zgodne z ustawieniami planu bazowego zabezpieczeń platformy Azure_ zawiera zestaw reguł opartych na zasadach grupy usługi Active Directory.

Większość ustawień jest dostępna jako parametry. Parametry umożliwiają dostosowanie tego, co jest poddane inspekcji. Dostosuj zasady do swoich wymagań lub zmapuj zasady na informacje innych firm, takie jak branżowe standardy regulacyjne.

Niektóre parametry obsługują zakres wartości całkowitej. Na przykład ustawienie Maksymalny wiek hasła może przeprowadzić inspekcję skutecznego ustawienia zasad grupy. Zakres "1,70" potwierdza, że użytkownicy są zobowiązani do zmiany haseł co najmniej co 70 dni, ale nie mniej niż jeden dzień.

Jeśli zasady są przypisywane przy użyciu szablonu wdrażania usługi Azure Resource Manager, użyj pliku parametrów do zarządzania wyjątkami. Zaewidencjonuj pliki do systemu kontroli wersji, takiego jak Git. Komentarze dotyczące zmian w pliku dostarczają dowodów na to, dlaczego przypisanie jest wyjątkiem od wartości oczekiwanej.

#### <a name="applying-configurations-using-guest-configuration"></a>Stosowanie konfiguracji przy użyciu konfiguracji gościa

Najnowsza funkcja usługi Azure Policy konfiguruje ustawienia wewnątrz komputerów. Definicja _Konfiguruj strefę czasową na komputerach z systemem Windows_ wprowadza zmiany na komputerze, konfigurując strefę czasową.

Podczas przypisywania definicji rozpoczynających się od _opcji Konfiguruj_należy również przypisać wymagania wstępne definicji _Wdrażanie, aby włączyć zasady konfiguracji gościa na maszynach wirtualnych systemu Windows_. W przypadku wybrania tej opcji można połączyć te definicje w inicjatywie.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Przypisywanie zasad do komputerów spoza platformy Azure

Zasady inspekcji dostępne dla konfiguracji gościa obejmują typ zasobu **Microsoft.HybridCompute/machines.** Wszystkie maszyny dołączane do [usługi Azure Arc dla serwerów,](../../../azure-arc/servers/overview.md) które znajdują się w zakresie przypisania zasad, są automatycznie uwzględniane.

### <a name="multiple-assignments"></a>Wiele przypisań

Zasady konfiguracji gościa obsługują obecnie tylko przypisywanie tego samego przypisania gościa raz na komputer, nawet jeśli przypisanie zasad używa różnych parametrów.

## <a name="client-log-files"></a>Pliki dziennika klienta

Rozszerzenie Konfiguracja gościa zapisuje pliki dziennika w następujących lokalizacjach:

Windows:`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux:`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Gdzie `<version>` odnosi się do bieżącego numeru wersji.

### <a name="collecting-logs-remotely"></a>Zdalne zbieranie dzienników

Pierwszym krokiem w rozwiązywaniu problemów z konfiguracją gościa konfiguracja lub moduły powinno być użycie `Test-GuestConfigurationPackage` polecenia cmdlet po instrukcje, jak utworzyć [niestandardowe zasady inspekcji konfiguracji gościa dla systemu Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Jeśli to się nie powiedzie, zbieranie dzienników klientów może pomóc w diagnozowaniu problemów.

#### <a name="windows"></a>Windows

Przechwytywanie informacji z plików dziennika przy użyciu [polecenia uruchamiania maszyny Wirtualnej platformy Azure](../../../virtual-machines/windows/run-command.md), poniższy przykład skrypt programu PowerShell może być pomocne.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Przechwytywanie informacji z plików dziennika przy użyciu [polecenia uruchamiania maszyny Wirtualnej platformy Azure](../../../virtual-machines/linux/run-command.md), poniższy przykład skrypt Bash może być pomocne.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Przykłady konfiguracji gościa

Wbudowane przykłady zasad konfiguracji gościa są dostępne w następujących lokalizacjach:

- [Wbudowane definicje zasad — konfiguracja gościa](../samples/built-in-policies.md#guest-configuration)
- [Wbudowane inicjatywy — konfiguracja gościa](../samples/built-in-initiatives.md#guest-configuration)
- [Przykłady usługi Azure Policy repo usługi GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak wyświetlić szczegóły każdego ustawienia w [widoku zgodności konfiguracji gościa](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Przejrzyj przykłady w [przykładach zasad platformy Azure](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo tworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [korygować niezgodne zasoby](../how-to/remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).

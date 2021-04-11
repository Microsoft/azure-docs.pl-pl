---
title: Role, uprawnienia i zabezpieczenia w Azure Monitor
description: Dowiedz się, jak używać wbudowanych ról i uprawnień Azure Monitor, aby ograniczyć dostęp do zasobów monitorowania.
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 64e770ea328516bdc9dbe1a2e17070c15a252fe7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490033"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Role, uprawnienia i zabezpieczenia w Azure Monitor

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wiele zespołów musi ściśle regulować dostęp do danych i ustawień monitorowania. Na przykład jeśli masz członków zespołu, którzy pracują wyłącznie w zakresie monitorowania (inżynierów pomocy technicznej, inżynierów DevOps) lub jeśli używasz dostawcy usług zarządzanych, możesz udzielić im dostępu tylko do monitorowania danych, jednocześnie ograniczając możliwość tworzenia, modyfikowania lub usuwania zasobów. W tym artykule pokazano, jak szybko zastosować wbudowaną rolę monitorowania roli platformy Azure dla użytkownika na platformie Azure lub utworzyć własną rolę niestandardową dla użytkownika, który wymaga ograniczonych uprawnień monitorowania. W tym artykule omówiono zagadnienia dotyczące zabezpieczeń zasobów związanych z Azure Monitor i sposób ograniczania dostępu do zawartych w nich danych.

## <a name="built-in-monitoring-roles"></a>Wbudowane role monitorowania
Wbudowane role Azure Monitor zaprojektowano w celu ułatwienia ograniczenia dostępu do zasobów w ramach subskrypcji, a jednocześnie włączenie tych osób odpowiedzialnych za monitorowanie infrastruktury w celu uzyskania i skonfigurowania potrzebnych danych. Azure Monitor udostępnia dwie wbudowane role: czytnik monitorowania i współautor monitorowania.

### <a name="monitoring-reader"></a>Czytnik monitorowania
Osoby, którym przypisano rolę czytelnik monitorowania, mogą wyświetlać wszystkie dane monitorowania w ramach subskrypcji, ale nie mogą modyfikować żadnych zasobów ani edytować żadnych ustawień związanych z zasobami monitorowania. Ta rola jest odpowiednia dla użytkowników w organizacji, takich jak pomoc techniczna lub inżynierowie operacyjni, którzy muszą mieć możliwość:

* Wyświetlanie pulpitów nawigacyjnych monitorowania w portalu i tworzenie własnych prywatnych pulpitów nawigacyjnych.
* Wyświetlanie reguł alertów zdefiniowanych w [alertach platformy Azure](alerts/alerts-overview.md)
* Zapytanie o metryki przy użyciu [interfejsu API REST Azure monitor](/rest/api/monitor/metrics), [poleceń cmdlet programu PowerShell](powershell-samples.md)lub [międzyplatformowego interfejsu wiersza polecenia](cli-samples.md).
* Wykonaj zapytanie dotyczące dziennika aktywności przy użyciu portalu, Azure Monitor interfejsu API REST, poleceń cmdlet programu PowerShell lub międzyplatformowego interfejsu wiersza polecenia.
* Wyświetlanie [ustawień diagnostycznych](essentials/diagnostic-settings.md) dla zasobu.
* Wyświetl [profil dziennika](essentials/activity-log.md#legacy-collection-methods) dla subskrypcji.
* Wyświetl ustawienia skalowania automatycznego.
* Wyświetlanie działań i ustawień alertów.
* Dostęp do danych Application Insights i wyświetlanie danych w usłudze AI Analytics.
* Wyszukaj Log Analytics dane obszaru roboczego, w tym dane użycia dla obszaru roboczego.
* Wyświetl Log Analytics grupy zarządzania.
* Pobierz schemat wyszukiwania w obszarze roboczym Log Analytics.
* Wyświetl listę pakietów monitorowania w obszarze roboczym Log Analytics.
* Pobierz i wykonaj zapisane wyszukiwania w obszarze roboczym Log Analytics.
* Pobierz konfigurację magazynu obszaru roboczego Log Analytics.

> [!NOTE]
> Ta rola nie daje dostępu do odczytu do danych dziennika przesyłanych strumieniowo do centrum zdarzeń lub przechowywanych na koncie magazynu. [Poniżej znajdują](#security-considerations-for-monitoring-data) się informacje dotyczące konfigurowania dostępu do tych zasobów.
> 
> 

### <a name="monitoring-contributor"></a>Współautor monitorowania
Osoby przypisane do roli współautor monitorowania mogą wyświetlać wszystkie dane monitorowania w ramach subskrypcji i tworzyć lub modyfikować ustawienia monitorowania, ale nie mogą modyfikować innych zasobów. Ta rola jest nadzbiorem roli czytelnik monitorowania i jest odpowiednia dla członków zespołu monitorowania organizacji lub dostawców usług zarządzanych, którzy oprócz powyższych uprawnień również muszą mieć możliwość:

* Publikuj pulpity nawigacyjne monitorowania jako udostępniony pulpit nawigacyjny.
* Ustawianie [ustawień diagnostycznych](essentials/diagnostic-settings.md) dla zasobu.\*
* Ustaw [profil dziennika](essentials/activity-log.md#legacy-collection-methods) dla subskrypcji.\*
* Skonfiguruj działanie i Ustawienia reguł alertów za pomocą [alertów platformy Azure](alerts/alerts-overview.md).
* Tworzenie Application Insightsych testów i składników sieci Web.
* Wyświetl listę kluczy wspólnych obszaru roboczego Log Analytics.
* Włączać lub wyłączać pakiety monitorowania w obszarze roboczym Log Analytics.
* Tworzenie i usuwanie zapisanych wyszukiwań w Log Analytics obszarze roboczym.
* Utwórz i Usuń konfigurację magazynu obszaru roboczego Log Analytics.

\*Użytkownik musi również oddzielnie udzielić uprawnienia ListKeys do zasobu docelowego (konto magazynu lub przestrzeń nazw centrum zdarzeń), aby ustawić profil dziennika lub ustawienie diagnostyczne.

> [!NOTE]
> Ta rola nie daje dostępu do odczytu do danych dziennika przesyłanych strumieniowo do centrum zdarzeń lub przechowywanych na koncie magazynu. [Poniżej znajdują](#security-considerations-for-monitoring-data) się informacje dotyczące konfigurowania dostępu do tych zasobów.
> 
> 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>Monitorowanie uprawnień i ról niestandardowych platformy Azure
Jeśli powyższe wbudowane role nie są zgodne z dokładnymi potrzebami zespołu, można [utworzyć rolę niestandardową platformy Azure](../role-based-access-control/custom-roles.md) z bardziej szczegółowymi uprawnieniami. Poniżej przedstawiono typowe operacje RBAC platformy Azure dotyczące Azure Monitor z ich opisami.

| Operacja | Opis |
| --- | --- |
| Microsoft. Insights/ActionGroups/[odczyt, zapis, usuwanie] |Grupy akcji do odczytu/zapisu/usuwania. |
| Microsoft. Insights/ActivityLogAlerts/[odczyt, zapis, usuwanie] |Odczyt/zapis/usuwanie alertów dziennika aktywności. |
| Microsoft. Insights/AlertRules/[odczyt, zapis, usuwanie] |Reguły alertów do odczytu/zapisu/usuwania (z alertów klasycznych). |
| Microsoft. Insights/AlertRules/incydenty/odczyt |Wyświetl listę incydentów (historia wyzwalanej reguły alertu) dla reguł alertów. Dotyczy to tylko portalu. |
| Microsoft. Insights/AutoscaleSettings/[odczyt, zapis, usuwanie] |Odczytaj/Zapisz/Usuń ustawienia skalowania automatycznego. |
| Microsoft. Insights/DiagnosticSettings/[odczyt, zapis, usuwanie] |Odczyt/zapis/usuwanie ustawień diagnostycznych. |
| Microsoft. Insights/EventCategories/Read |Wylicz wszystkie kategorie możliwe w dzienniku aktywności. Używane przez Azure Portal. |
| Microsoft. Insights/eventtypes/digestevents/Read |To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. |
| Microsoft. Insights/eventtypes/Values/odczyty |Wyświetl listę zdarzeń dziennika aktywności (zdarzenia zarządzania) w subskrypcji. To uprawnienie dotyczy zarówno dostępu programowego, jak i portalu do dziennika aktywności. |
| Microsoft. Insights/ExtendedDiagnosticSettings/[odczyt, zapis, usuwanie] | Odczyt/zapis/usuwanie ustawień diagnostycznych dla dzienników przepływów sieciowych. |
| Microsoft. Insights/LogDefinitions/Read |To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. |
| Microsoft. Insights/LogProfiles/[odczyt, zapis, usuwanie] |Odczyt/zapis/usuwanie profilów dzienników (przesyłanie strumieniowe dziennika aktywności do centrum zdarzeń lub konta magazynu). |
| Microsoft. Insights/MetricAlerts/[odczyt, zapis, usuwanie] |Odczytaj/Zapisz/Usuń alerty metryk niemal w czasie rzeczywistym |
| Microsoft. Insights/MetricDefinitions/Read |Odczytaj definicje metryk (lista dostępnych typów metryk dla zasobu). |
| Microsoft. Insights/Metrics/Read |Odczytaj metryki dla zasobu. |
| Microsoft. Insights/Register/Action |Zarejestruj dostawcę zasobów Azure Monitor. |
| Microsoft. Insights/ScheduledQueryRules/[odczyt, zapis, usuwanie] |Odczytaj/Zapisz/Usuń alerty dzienników w Azure Monitor. |



> [!NOTE]
> Dostęp do alertów, ustawień diagnostycznych i metryk dla zasobu wymaga, aby użytkownik miał dostęp do odczytu dla typu zasobu i zakresu tego zasobu. Tworzenie ("Write") ustawień diagnostycznych lub profilu dziennika, które są archiwizowane na koncie magazynu lub strumieniach do centrów zdarzeń, wymagają również, aby użytkownik miał uprawnienie ListKeys do zasobu docelowego.
> 
> 

Na przykład korzystając z powyższej tabeli, można utworzyć rolę niestandardową platformy Azure dla "czytnika dzienników aktywności" w następujący sposób:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Zagadnienia związane z zabezpieczeniami dotyczące monitorowania danych
Monitorowanie danych — w szczególności plików dziennika — może zawierać informacje poufne, takie jak adresy IP lub nazwy użytkowników. Dane monitorowania z platformy Azure są dostępne w trzech podstawowych formach:

1. Dziennik aktywności, który opisuje wszystkie działania płaszczyzny kontroli w ramach subskrypcji platformy Azure.
2. Dzienniki zasobów, które są emitowane przez zasób.
3. Metryki, które są emitowane przez zasoby.

Wszystkie trzy z tych typów danych mogą być przechowywane na koncie magazynu lub przesyłane strumieniowo do centrum zdarzeń, które są zasobami platformy Azure ogólnego przeznaczenia. Ponieważ są to zasoby ogólnego przeznaczenia, tworzenie, usuwanie i uzyskiwanie do nich dostępu, są uprzywilejowaną operacją zarezerwowaną dla administratora. Zalecamy stosowanie następujących rozwiązań do monitorowania zasobów związanych z monitorowaniem w celu zapobiegania nadużyciom:

* Do monitorowania danych służy jedno dedykowane konto magazynu. Jeśli konieczne jest oddzielenie danych monitorowania na wiele kont magazynu, nigdy nie należy udostępniać konta magazynu między danymi monitorowania i niemonitorowania, ponieważ może to spowodować nieumyślne udzielenie dostępu do danych monitorowania (na przykład SIEM innej firmy) do danych niezwiązanych z monitorowaniem.
* Użyj pojedynczej, dedykowanej Service Bus lub przestrzeni nazw centrum zdarzeń dla wszystkich ustawień diagnostycznych z tego samego powodu, jak powyżej.
* Ograniczanie dostępu do kont magazynu związanych z monitorowaniem lub centrów zdarzeń przez utrzymywanie ich w osobnej grupie zasobów i [Używanie zakresu](../role-based-access-control/overview.md#scope) w rolach monitorowania w celu ograniczenia dostępu tylko do tej grupy zasobów.
* Nie udzielaj uprawnienia ListKeys dla kont magazynu ani centrów zdarzeń w zakresie subskrypcji, gdy użytkownik potrzebuje tylko dostępu do danych monitorowania. Zamiast tego nadaj temu użytkownikowi uprawnienia do zasobu lub grupy zasobów (Jeśli masz dedykowaną grupę zasobów monitorowania).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Ograniczanie dostępu do kont magazynu związanych z monitorowaniem
Gdy użytkownik lub aplikacja musi mieć dostęp do danych monitorowania na koncie magazynu, należy [wygenerować sygnaturę dostępu współdzielonego konta](/rest/api/storageservices/create-account-sas) na koncie magazynu zawierającym dane monitorowania z dostępem tylko do odczytu do usługi BLOB Storage. W programie PowerShell może wyglądać następująco:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Następnie można przekazać token do jednostki, która musi odczytywać z tego konta magazynu, a także wyświetlać i odczytywać wszystkie obiekty blob w ramach tego konta magazynu.

Alternatywnie, jeśli konieczne jest kontrolowanie tego uprawnienia za pomocą usługi Azure RBAC, można przyznać tej jednostce uprawnienia Microsoft. Storage/storageAccounts/ListKeys/Action na tym konkretnym koncie magazynu. Jest to konieczne w przypadku użytkowników, którzy muszą mieć możliwość ustawienia ustawień diagnostycznych lub profilu dziennika na potrzeby archiwizowania na koncie magazynu. Można na przykład utworzyć następującą rolę niestandardową platformy Azure dla użytkownika lub aplikacji, która musi tylko odczytać z jednego konta magazynu:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> Uprawnienie ListKeys umożliwia użytkownikowi wyświetlanie kluczy podstawowych i pomocniczych kont magazynu. Te klucze przyznają użytkownikowi wszystkie podpisane uprawnienia (odczyt, zapis, tworzenie obiektów blob, usuwanie obiektów BLOB itp.) dla wszystkich podpisanych usług (obiektów blob, kolejek, tabel, plików) na tym koncie magazynu. Zalecamy używanie sygnatury dostępu współdzielonego konta opisanego powyżej, jeśli jest to możliwe.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Ograniczanie dostępu do centrów zdarzeń związanych z monitorowaniem
Podobny wzorzec może być stosowany w przypadku centrów zdarzeń, ale najpierw należy utworzyć dedykowaną regułę autoryzacji nasłuchiwania. Aby udzielić dostępu do aplikacji, która musi nasłuchiwać tylko centrów zdarzeń związanych z monitorowaniem, wykonaj następujące czynności:

1. Utwórz zasady dostępu współdzielonego dla centrów zdarzeń utworzonych na potrzeby przesyłania strumieniowego danych monitorowania tylko przy użyciu oświadczeń nasłuchujących. Można to zrobić w portalu. Można na przykład wywołać "monitoringReadOnly". Jeśli to możliwe, należy przekazać ten klucz bezpośrednio do konsumenta i pominąć następny krok.
2. Jeśli konsument musi mieć możliwość uzyskania klucza ad hoc, Udziel użytkownikowi akcji ListKeys dla tego centrum zdarzeń. Jest to również konieczne w przypadku użytkowników, którzy muszą mieć możliwość ustawienia ustawień diagnostycznych lub profilu dziennika na potrzeby przesyłania strumieniowego do centrów zdarzeń. Na przykład możesz utworzyć regułę RBAC platformy Azure:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitorowanie w zabezpieczonej sieci wirtualnej

Azure Monitor potrzebuje dostępu do zasobów platformy Azure w celu zapewnienia usług, które są włączone. Jeśli chcesz monitorować zasoby platformy Azure przy zachowaniu ich dostępu do publicznego Internetu, możesz włączyć poniższe ustawienia.

### <a name="secured-storage-accounts"></a>Zabezpieczone konta magazynu 

Dane monitorowania są często zapisywane na koncie magazynu. Można upewnić się, że dane skopiowane na konto magazynu nie są dostępne dla nieautoryzowanych użytkowników. Aby uzyskać dodatkowe zabezpieczenia, można zablokować dostęp do sieci, aby zezwolić na dostęp do konta magazynu tylko autoryzowanym zasobom i zaufanym usługom firmy Microsoft, ograniczając konto magazynu do używania "wybranych sieci".
![Okno dialogowe ustawień usługi Azure Storage ](./media/roles-permissions-security/secured-storage-example.png) Azure monitor jest uznawane za jedną z następujących "zaufanych usług firmy Microsoft", Jeśli zezwolisz zaufanym usługom firmy Microsoft na dostęp do bezpiecznego magazynu, usługa Azure monitor będzie mieć dostęp do bezpiecznego konta magazynu, co umożliwia zapisywanie Azure monitor dzienników zasobów, dziennika aktywności i metryk na koncie magazynu w ramach tych chronionych warunków. Spowoduje to również umożliwienie Log Analytics odczytywania dzienników z bezpiecznego magazynu.   


Aby uzyskać więcej informacji, zobacz [zabezpieczenia sieci i usługa Azure Storage](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Następne kroki
* [Przeczytaj informacje o RBAC i uprawnieniach platformy Azure w Menedżer zasobów](../role-based-access-control/overview.md)
* [Zapoznaj się z omówieniem monitorowania na platformie Azure](overview.md)


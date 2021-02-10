---
title: Często zadawane pytania i znane problemy związane z tożsamościami zarządzanymi — Azure AD
description: Znane problemy związane z tożsamościami zarządzanymi dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/04/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f1be2e64435cb0bcdb369a398a9a65fc3714fb2
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008540"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Często zadawane pytania i znane problemy związane z tożsamościami zarządzanymi dla zasobów platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Jak można znaleźć zasoby, które mają zarządzaną tożsamość?

Listę zasobów, które mają tożsamość zarządzaną przypisaną przez system, można znaleźć za pomocą następującego polecenia platformy Azure: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>Czy zarządzane tożsamości mają obiekt aplikacji zapasowej?

Nie. Tożsamości zarządzane i rejestracje aplikacja usługi Azure AD nie są takie same w katalogu. 

Rejestracje aplikacji mają dwa składniki: obiekt aplikacji + obiekt główny usługi. Zarządzane tożsamości dla zasobów platformy Azure mają tylko jeden z tych składników: obiekt główny usługi. 

Tożsamości zarządzane nie mają obiektu aplikacji w katalogu, który jest często używany do przyznawania uprawnień aplikacji dla programu MS Graph. Zamiast tego należy przydzielić uprawnienia programu MS Graph do zarządzanych tożsamości bezpośrednio do jednostki usługi.  

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Czy można używać tej samej tożsamości zarządzanej w wielu regionach?

W krótkim przypadku można użyć tożsamości zarządzanych przez użytkownika w więcej niż jednym regionie świadczenia usługi Azure. Dłuższa odpowiedź polega na tym, że podczas tworzenia tożsamości zarządzanych przez użytkownika jako zasobów regionalnych skojarzona jednostka [usługi](../develop/app-objects-and-service-principals.md#service-principal-object) (SPN) utworzona w usłudze Azure AD jest dostępna globalnie. Nazwy głównej usługi można użyć z dowolnego regionu platformy Azure, a jej dostępność zależy od dostępności usługi Azure AD. Jeśli na przykład została utworzona tożsamość zarządzana przypisana przez użytkownika w regionie South-Central i ten region stał się niedostępny, problem ma wpływ tylko na działania [płaszczyzny kontroli](../../azure-resource-manager/management/control-plane-and-data-plane.md) w samej tożsamości zarządzanej.  Nie wpłynie to na działania wykonywane przez wszystkie zasoby, które zostały już skonfigurowane do korzystania z zarządzanych tożsamości.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Czy zarządzane tożsamości dla zasobów platformy Azure współpracują z usługą Azure Cloud Services?

Nie. nie ma żadnych planów do obsługi tożsamości zarządzanych dla zasobów platformy Azure w usłudze Azure Cloud Services.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Co to jest poświadczenie skojarzone z zarządzaną tożsamością? Jak długo jest ważna i jak często jest obracana?

> [!NOTE]
> Jak są uwierzytelniane tożsamości zarządzane, szczegóły implementacji wewnętrznej mogą ulec zmianie bez powiadomienia.

Zarządzane tożsamości korzystają z uwierzytelniania opartego na certyfikatach. Każde poświadczenie zarządzanej tożsamości ma wygaśnięcie 90 dni i jest wycofywane po 45 dniach.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Jaka jest granica zabezpieczeń zarządzanych tożsamości dla zasobów platformy Azure?

Granica zabezpieczeń tożsamości jest zasobem, do którego jest dołączony. Na przykład granica zabezpieczeń maszyny wirtualnej z zarządzanymi tożsamościami dla zasobów platformy Azure to maszyna wirtualna. Każdy kod uruchomiony na tej maszynie wirtualnej może wywoływać zarządzane tożsamości dla punktu końcowego zasobów platformy Azure i tokenów żądań. Jest to podobne środowisko z innymi zasobami, które obsługują zarządzane tożsamości dla zasobów platformy Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jaką tożsamość będzie IMDS domyślnie, jeśli nie określisz tożsamości w żądaniu?

- Jeśli jest włączona tożsamość zarządzana przypisana przez system i żadna tożsamość nie została określona w żądaniu, IMDS domyślnie zostanie skojarzona z tożsamością zarządzaną przez system.
- Jeśli tożsamość zarządzana przypisana przez system nie jest włączona i istnieje tylko jedna tożsamość zarządzana przypisana przez użytkownika, IMDS zostanie domyślnie przypisana tożsamość zarządzana pojedynczego użytkownika. 
- Jeśli tożsamość zarządzana przypisana przez system nie jest włączona, a istnieją wiele tożsamości zarządzanych przypisanych przez użytkownika, a następnie wymagane jest określenie tożsamości zarządzanej w żądaniu.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Czy tożsamość zarządzana zostanie automatycznie utworzona, Jeśli przeniesiesz subskrypcję do innego katalogu?

Nie. Jeśli przeniesiesz subskrypcję do innego katalogu, musisz ręcznie utworzyć ponownie i przydzielić przydziały ról platformy Azure.
- Dla tożsamości zarządzanych przypisanych przez system: Wyłącz i ponownie włącz. 
- W przypadku tożsamości zarządzanych przypisanych przez użytkownika: Usuń, ponownie utwórz i Dołącz je do niezbędnych zasobów (np. maszyn wirtualnych)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Czy mogę użyć tożsamości zarządzanej w celu uzyskania dostępu do zasobu w innym katalogu/dzierżawie?

Nie. Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Jakie uprawnienia RBAC platformy Azure są wymagane do zarządzania tożsamościami w zasobie? 

- Tożsamość zarządzana przypisana przez system: wymagane są uprawnienia do zapisu dla zasobu. Na przykład w przypadku maszyn wirtualnych potrzebne jest uprawnienie Microsoft.Compute/virtualMachines/write. Ta akcja jest uwzględniona w rolach wbudowanych specyficznych dla zasobów, takich jak [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Tożsamość zarządzana przypisana przez użytkownika: wymagane są uprawnienia do zapisu dla zasobu. Na przykład w przypadku maszyn wirtualnych potrzebne jest uprawnienie Microsoft.Compute/virtualMachines/write. Oprócz przypisywania roli [operatora tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) przez zarządzaną tożsamość.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Jak mogę uniemożliwić tworzenie tożsamości zarządzanych przypisanych przez użytkownika?

Możesz uniemożliwić użytkownikom tworzenie tożsamości zarządzanych przez użytkowników przy użyciu [Azure Policy](../../governance/policy/overview.md)

- Przejdź do [Azure Portal](https://portal.azure.com) i przejdź do pozycji **zasady**.
- Wybieranie **definicji**
- Wybierz pozycję **+ Definicja zasad** , a następnie wprowadź niezbędne informacje.
- W sekcji Reguła zasad wklej

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {}
}

```

Po utworzeniu zasad Przypisz je do grupy zasobów, której chcesz użyć.

- Przejdź do grup zasobów.
- Znajdź grupę zasobów używaną do testowania.
- Wybierz pozycję **zasady** w menu po lewej stronie.
- Wybierz pozycję **Przypisz zasady**
- Sekcja **podstawowe informacje** zawiera:
    - **Zakres** Grupa zasobów używana do testowania
    - **Definicja zasad**: utworzone wcześniej zasady.
- Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz kolejno pozycje **Przegląd + Utwórz**

W tym momencie każda próba utworzenia tożsamości zarządzanej przypisanej przez użytkownika w grupie zasobów zakończy się niepowodzeniem.

  ![Naruszenie zasad](./media/known-issues/policy-violation.png)

## <a name="known-issues"></a>Znane problemy

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Skrypt automatyzacji" kończy się niepowodzeniem podczas próby eksportowania schematu dla tożsamości zarządzanych dla rozszerzenia zasobów platformy Azure

Gdy zarządzane tożsamości dla zasobów platformy Azure są włączone na maszynie wirtualnej, podczas próby użycia funkcji "skrypt automatyzacji" dla maszyny wirtualnej lub jej grupy zasobów jest wyświetlany następujący błąd:

![Tożsamość zarządzana dla błędu eksportowania skryptu automatyzacji zasobów platformy Azure](./media/msi-known-issues/automation-script-export-error.png)

Zarządzane tożsamości dla rozszerzenia maszyny wirtualnej zasobów platformy Azure (planowane do wycofania w styczniu 2019) nie obsługują obecnie możliwości eksportowania jego schematu do szablonu grupy zasobów. W związku z tym wygenerowany szablon nie pokazuje parametrów konfiguracji, aby umożliwić zarządzanie tożsamościami dla zasobów platformy Azure w ramach zasobu. Te sekcje można dodać ręcznie, postępując zgodnie z przykładami w temacie [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu szablonów](qs-configure-template-windows-vm.md).

Po udostępnieniu funkcji eksportu schematu dla tożsamości zarządzanych dla rozszerzenia maszyny wirtualnej Azure Resources (planowanej do wycofania w styczniu 2019) zostanie ono wyświetlone w obszarze [Eksportowanie grup zasobów zawierających rozszerzenia maszyn wirtualnych](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Nie można uruchomić maszyny wirtualnej po przeniesieniu jej z grupy zasobów lub subskrypcji

Jeśli przeniesiesz maszynę wirtualną w stan uruchomienia, będzie ona nadal działać podczas przenoszenia. Jednak po przeniesieniu, jeśli maszyna wirtualna zostanie zatrzymana i uruchomiona ponownie, nie zostanie uruchomiona. Ten problem występuje, ponieważ maszyna wirtualna nie aktualizuje odwołania do zarządzanych tożsamości dla tożsamości zasobów platformy Azure i kontynuuje wskazywanie jej w starej grupie zasobów.

**Obejście** 
 
Wyzwól aktualizację na maszynie wirtualnej, aby możliwe było uzyskanie prawidłowych wartości dla zarządzanych tożsamości dla zasobów platformy Azure. Aby zaktualizować odwołanie do zarządzanych tożsamości dla tożsamości zasobów platformy Azure, można zmienić wartość właściwości maszyny wirtualnej. Na przykład można ustawić nową wartość tagu na maszynie wirtualnej przy użyciu następującego polecenia:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
To polecenie ustawia nowy tag "fixVM" o wartości 1 na maszynie wirtualnej. 
 
Ustawiając tę właściwość, maszyna wirtualna ma aktualizacje z poprawnymi tożsamościami zarządzanymi dla identyfikatora URI zasobu zasobów platformy Azure, a następnie powinno być możliwe uruchomienie maszyny wirtualnej. 
 
Po uruchomieniu maszyny wirtualnej tag można usunąć za pomocą następującego polecenia:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Przenoszenie subskrypcji między katalogami usługi Azure AD

Tożsamości zarządzane nie są aktualizowane, gdy subskrypcja jest przenoszona/przenoszona do innego katalogu. W związku z tym wszystkie istniejące tożsamości zarządzane przypisane do systemu lub przypisane przez użytkownika zostaną przerwane. 

Obejście dla zarządzanych tożsamości w ramach subskrypcji, która została przeniesiona do innego katalogu:

 - Dla tożsamości zarządzanych przypisanych przez system: Wyłącz i ponownie włącz. 
 - W przypadku tożsamości zarządzanych przypisanych przez użytkownika: Usuń, ponownie utwórz i Dołącz je do niezbędnych zasobów (np. maszyn wirtualnych)

Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD](../../role-based-access-control/transfer-subscription.md).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Przeniesienie tożsamości zarządzanej przypisanej przez użytkownika do innej grupy zasobów/subskrypcji

Przeniesienie tożsamości zarządzanej przypisanej przez użytkownika do innej grupy zasobów nie jest obsługiwane.

---
title: Zarządzane tożsamości dla zasobów platformy Azure — często zadawane pytania — Azure AD
description: Często zadawane pytania dotyczące tożsamości zarządzanych
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.openlocfilehash: 3d3ab9859eb9f85d5ca7d0573fa79443ae9fe964
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251009"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Zarządzane tożsamości dla zasobów platformy Azure — często zadawane pytania — Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

## <a name="administration"></a>Administracja

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Jak można znaleźć zasoby, które mają zarządzaną tożsamość?

Listę zasobów, które mają tożsamość zarządzaną przypisaną przez system, można znaleźć za pomocą następującego polecenia platformy Azure: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Jakie uprawnienia RBAC platformy Azure są wymagane do zarządzania tożsamościami w zasobie? 

- Tożsamość zarządzana przypisana przez system: wymagane są uprawnienia do zapisu dla zasobu. Na przykład w przypadku maszyn wirtualnych potrzebne jest uprawnienie Microsoft.Compute/virtualMachines/write. Ta akcja jest uwzględniona w rolach wbudowanych specyficznych dla zasobów, takich jak [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Tożsamość zarządzana przypisana przez użytkownika: wymagane są uprawnienia do zapisu dla zasobu. Na przykład w przypadku maszyn wirtualnych potrzebne jest uprawnienie Microsoft.Compute/virtualMachines/write. Oprócz przypisywania roli [operatora tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) przez zarządzaną tożsamość.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Jak mogę uniemożliwić tworzenie tożsamości zarządzanych przypisanych przez użytkownika?

Możesz uniemożliwić użytkownikom tworzenie tożsamości zarządzanych przez użytkowników przy użyciu [Azure Policy](../../governance/policy/overview.md)

1. Przejdź do [Azure Portal](https://portal.azure.com) i przejdź do pozycji **zasady**.
2. Wybieranie **definicji**
3. Wybierz pozycję **+ Definicja zasad** , a następnie wprowadź niezbędne informacje.
4. W sekcji Reguła zasad wklej
    
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

1. Przejdź do grup zasobów.
2. Znajdź grupę zasobów używaną do testowania.
3. Wybierz pozycję **zasady** w menu po lewej stronie.
4. Wybierz pozycję **Przypisz zasady**
5. W sekcji **podstawowe informacje** zapewniają:
    1. **Zakres** Grupa zasobów używana do testowania
    1. **Definicja zasad**: utworzone wcześniej zasady.
6. Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz kolejno pozycje **Przegląd + Utwórz**

W tym momencie każda próba utworzenia tożsamości zarządzanej przypisanej przez użytkownika w grupie zasobów zakończy się niepowodzeniem.

  ![Naruszenie zasad](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Pojęcia

### <a name="do-managed-identities-have-a-backing-app-object"></a>Czy zarządzane tożsamości mają obiekt aplikacji zapasowej?

Nie. Tożsamości zarządzane i rejestracje aplikacja usługi Azure AD nie są takie same w katalogu.

Rejestracje aplikacji mają dwa składniki: obiekt aplikacji + obiekt główny usługi.
Zarządzane tożsamości dla zasobów platformy Azure mają tylko jeden z tych składników: obiekt główny usługi.

Tożsamości zarządzane nie mają obiektu aplikacji w katalogu, który jest często używany do przyznawania uprawnień aplikacji dla programu MS Graph. Zamiast tego należy przydzielić uprawnienia programu MS Graph do zarządzanych tożsamości bezpośrednio do jednostki usługi.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Co to jest poświadczenie skojarzone z zarządzaną tożsamością? Jak długo jest ważna i jak często jest obracana?

> [!NOTE]
> Jak są uwierzytelniane tożsamości zarządzane, szczegóły implementacji wewnętrznej mogą ulec zmianie bez powiadomienia.

Zarządzane tożsamości korzystają z uwierzytelniania opartego na certyfikatach. Każde poświadczenie zarządzanej tożsamości ma wygaśnięcie 90 dni i jest wycofywane po 45 dniach.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jaką tożsamość będzie IMDS domyślnie, jeśli nie określisz tożsamości w żądaniu?

- Jeśli jest włączona tożsamość zarządzana przypisana przez system i żadna tożsamość nie została określona w żądaniu, IMDS domyślnie przypisana tożsamość zarządzana przez system.
- Jeśli tożsamość zarządzana przypisana przez system nie jest włączona, a istnieje tylko jedna tożsamość zarządzana przypisana przez użytkownika, IMDS domyślnie zostanie skojarzona tożsamość zarządzana pojedynczego użytkownika.
- Jeśli tożsamość zarządzana przypisana przez system nie jest włączona i istnieje wiele tożsamości zarządzanych przypisanych przez użytkownika, w żądaniu należy określić zarządzaną tożsamość.

## <a name="limitations"></a>Ograniczenia

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Czy można używać tej samej tożsamości zarządzanej w wielu regionach?

W krótkim przypadku można użyć tożsamości zarządzanych przez użytkownika w więcej niż jednym regionie świadczenia usługi Azure. Dłuższa odpowiedź polega na tym, że podczas tworzenia tożsamości zarządzanych przez użytkownika jako zasobów regionalnych skojarzona jednostka [usługi](../develop/app-objects-and-service-principals.md#service-principal-object) (SP) utworzona w usłudze Azure AD jest dostępna globalnie. Nazwy głównej usługi można użyć z dowolnego regionu platformy Azure, a jej dostępność zależy od dostępności usługi Azure AD. Jeśli na przykład została utworzona tożsamość zarządzana przypisana przez użytkownika w regionie South-Central i ten region stał się niedostępny, problem ma wpływ tylko na działania [płaszczyzny kontroli](../../azure-resource-manager/management/control-plane-and-data-plane.md) w samej tożsamości zarządzanej.  Nie wpłynie to na działania wykonywane przez wszystkie zasoby, które zostały już skonfigurowane do korzystania z zarządzanych tożsamości.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Czy zarządzane tożsamości dla zasobów platformy Azure współpracują z usługą Azure Cloud Services?

Nie. nie ma żadnych planów do obsługi tożsamości zarządzanych dla zasobów platformy Azure w usłudze Azure Cloud Services.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Jaka jest granica zabezpieczeń zarządzanych tożsamości dla zasobów platformy Azure?

Granica zabezpieczeń tożsamości jest zasobem, do którego jest dołączony. Na przykład granica zabezpieczeń maszyny wirtualnej z zarządzanymi tożsamościami dla zasobów platformy Azure to maszyna wirtualna. Każdy kod uruchomiony na tej maszynie wirtualnej może wywoływać zarządzane tożsamości dla punktu końcowego zasobów platformy Azure i tokenów żądań. Jest to podobne środowisko z innymi zasobami, które obsługują zarządzane tożsamości dla zasobów platformy Azure.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Czy tożsamość zarządzana zostanie automatycznie utworzona, Jeśli przeniesiesz subskrypcję do innego katalogu?

Nie. Jeśli przeniesiesz subskrypcję do innego katalogu, musisz ręcznie utworzyć ponownie i przydzielić przydziały ról platformy Azure.
- Dla tożsamości zarządzanych przypisanych przez system: Wyłącz i ponownie włącz. 
- W przypadku tożsamości zarządzanych przypisanych przez użytkownika: Usuń, ponownie utwórz i Dołącz je do niezbędnych zasobów (np. maszyn wirtualnych)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Czy mogę użyć tożsamości zarządzanej w celu uzyskania dostępu do zasobu w innym katalogu/dzierżawie?

Nie. Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Czy istnieją limity szybkości, które mają zastosowanie do tożsamości zarządzanych?

Limity zarządzanych tożsamości mają zależności od limitów usługi platformy Azure, limitów platformy Azure Instance Metadata Service (IMDS) i Azure Active Directory limitów usługi.

- **Limity usługi platformy Azure** definiują liczbę operacji tworzenia, które mogą być wykonywane na poziomach dzierżawy i subskrypcji. Tożsamość zarządzana przypisana przez użytkownika ma także [ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) dotyczące ich nazw.
- **IMDS** Ogólnie rzecz biorąc, żądania do IMDS są ograniczone do pięciu żądań na sekundę. Żądania przekraczające wartość progową zostaną odrzucone z 429 odpowiedziami. Żądania do kategorii tożsamości zarządzanej są ograniczone do 20 żądań na sekundę i 5 współbieżnych żądań. Więcej informacji można znaleźć w artykule [usługi metadanych wystąpienia platformy Azure](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity) .
- **Usługa Azure Active Directory** Każda tożsamość zarządzana liczy się do limitu przydziału obiektów w dzierżawie usługi Azure AD zgodnie z opisem w temacie [limity i ograniczenia usług Azure AD](../enterprise-users/directory-service-limits-restrictions.md).


## <a name="is-it-ok-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Czy chcesz przenieść tożsamość zarządzaną przypisaną przez użytkownika do innej grupy zasobów/subskrypcji?

Przeniesienie tożsamości zarządzanej przypisanej przez użytkownika do innej grupy zasobów nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak zarządzane tożsamości działają z maszynami wirtualnymi](how-managed-identities-work-vm.md)

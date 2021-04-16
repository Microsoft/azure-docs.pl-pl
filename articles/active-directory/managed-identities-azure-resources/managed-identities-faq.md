---
title: Tożsamości zarządzane dla zasobów platformy Azure — często zadawane pytania — Azure AD
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
ms.openlocfilehash: 07b106630cffae75c5e4588d14de7ae938945614
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534119"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Tożsamości zarządzane dla zasobów platformy Azure — często zadawane pytania — Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

## <a name="administration"></a>Administracja

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Jak można znaleźć zasoby, które mają tożsamość zarządzaną?

Listę zasobów, które mają tożsamość zarządzaną przypisaną przez system, można znaleźć za pomocą następującego polecenia interfejsu wiersza polecenia platformy Azure: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Jakie uprawnienia RBAC platformy Azure są wymagane do zarządzania tożsamością w zasobie? 

- Tożsamość zarządzana przypisana przez system: musisz mieć uprawnienia do zapisu w zasobie. Na przykład w przypadku maszyn wirtualnych potrzebne jest uprawnienie Microsoft.Compute/virtualMachines/write. Ta akcja jest uwzględniona w rolach wbudowanych specyficznych dla zasobu, takich jak [Współautor maszyny wirtualnej.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
- Tożsamość zarządzana przypisana przez użytkownika: musisz mieć uprawnienia do zapisu w zasobie. Na przykład w przypadku maszyn wirtualnych potrzebne jest uprawnienie Microsoft.Compute/virtualMachines/write. Oprócz przypisania roli [Operator tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) do tożsamości zarządzanej.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Jak mogę tworzenie tożsamości zarządzanych przypisanych przez użytkownika?

Możesz chronić użytkowników przed tworzeniem tożsamości zarządzanych przypisanych przez użytkownika przy [użyciu Azure Policy](../../governance/policy/overview.md)

1. Przejdź do [Azure Portal](https://portal.azure.com) i przejdź do **zasad**.
2. Wybieranie **definicji**
3. Wybierz **pozycję + Definicja zasad** i wprowadź niezbędne informacje.
4. Wklej sekcję reguły zasad
    
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

Po utworzeniu zasad przypisz je do grupy zasobów, która ma być używana.

1. Przejdź do grup zasobów.
2. Znajdź grupę zasobów używaną do testowania.
3. Wybierz **pozycję Zasady** z menu po lewej stronie.
4. Wybierz **pozycję Przypisz zasady**
5. W **sekcji Podstawy** podaj:
    1. **Zakres** Grupa zasobów, która jest używana do testowania
    1. **Definicja zasad:** zasady utworzone wcześniej.
6. Pozostaw wartości domyślne wszystkich pozostałych ustawień i wybierz pozycję **Przejrzyj i utwórz**

W tym momencie próba utworzenia tożsamości zarządzanej przypisanej przez użytkownika w grupie zasobów nie powiedzie się.

  ![Naruszenie zasad](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Pojęcia

### <a name="do-managed-identities-have-a-backing-app-object"></a>Czy tożsamości zarządzane mają zapasowy obiekt aplikacji?

Nie. Tożsamości zarządzane i aplikacja usługi Azure AD rejestracji nie są tym samym co w katalogu.

Rejestracje aplikacji dwa składniki: obiekt aplikacji i obiekt jednostki usługi.
Tożsamości zarządzane dla zasobów platformy Azure mają tylko jeden z tych składników: obiekt jednostki usługi.

Tożsamości zarządzane nie mają obiektu aplikacji w katalogu , co jest często używane do udzielania uprawnień aplikacji dla programu MS Graph. Zamiast tego uprawnienia programu MS Graph dla tożsamości zarządzanych należy przyznać bezpośrednio do jednostki usługi.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Jakie poświadczenia są skojarzone z tożsamością zarządzaną? Jak długo jest prawidłowa i jak często jest obracana?

> [!NOTE]
> Sposób uwierzytelniania tożsamości zarządzanych to szczegóły implementacji wewnętrznej, które mogą ulec zmianie bez powiadomienia.

Tożsamości zarządzane korzystają z uwierzytelniania opartego na certyfikatach. Każde poświadczenie tożsamości zarządzanej wygasa przez 90 dni i jest wycofywowane po 45 dniach.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jaka tożsamość będzie domyślnie określana przez IMDS, jeśli nie określi ona tożsamości w żądaniu?

- Jeśli tożsamość zarządzana przypisana przez system jest włączona i nie określono tożsamości w żądaniu, funkcja IMDS domyślnie określa tożsamość zarządzaną przypisaną przez system.
- Jeśli tożsamość zarządzana przypisana przez system nie jest włączona i istnieje tylko jedna tożsamość zarządzana przypisana przez użytkownika, usługi IMDS domyślnie przypisze do tej tożsamości zarządzanej przypisanej przez jednego użytkownika.
- Jeśli tożsamość zarządzana przypisana przez system nie jest włączona i istnieje wiele tożsamości zarządzanych przypisanych przez użytkownika, musisz określić tożsamość zarządzaną w żądaniu.

## <a name="limitations"></a>Ograniczenia

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Czy ta sama tożsamość zarządzana może być używana w wielu regionach?

Krótko mówiąc, tak, możesz używać tożsamości zarządzanych przypisanych przez użytkownika w więcej niż jednym regionie świadczenia usługi Azure. Dłuższe odpowiedzią jest to, że chociaż tożsamości zarządzane przypisane przez użytkownika są tworzone jako zasoby regionalne, skojarzona nazwa główna [usługi](../develop/app-objects-and-service-principals.md#service-principal-object) (SP) utworzona w usłudze Azure AD jest dostępna globalnie. Jednostki usługi można używać z dowolnego regionu świadczenia usługi Azure, a jej dostępność zależy od dostępności usługi Azure AD. Jeśli na przykład utworzono tożsamość zarządzaną przypisaną przez użytkownika w regionie South-Central, [](../../azure-resource-manager/management/control-plane-and-data-plane.md) a ten region stanie się niedostępny, ten problem będzie miał wpływ tylko na działania płaszczyzny sterowania w samej tożsamości zarządzanej.  Nie ma to wpływu na działania wykonywane przez zasoby już skonfigurowane do używania tożsamości zarządzanych.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Czy tożsamości zarządzane dla zasobów platformy Azure działają z Azure Cloud Services?

Nie, nie ma planów obsługi tożsamości zarządzanych dla zasobów platformy Azure w Azure Cloud Services.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Jaka jest granica zabezpieczeń tożsamości zarządzanych dla zasobów platformy Azure?

Granica zabezpieczeń tożsamości jest zasobem, do którego jest dołączona. Na przykład granicą zabezpieczeń dla maszyny wirtualnej z włączonymi tożsamościami zarządzanymi dla zasobów platformy Azure jest maszyna wirtualna. Dowolny kod uruchomiony na tej maszynie wirtualnej może wywołać tożsamości zarządzane dla punktu końcowego zasobów platformy Azure i zażądać tokenów. Jest to podobne środowisko z innymi zasobami, które obsługują tożsamości zarządzane dla zasobów platformy Azure.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Czy tożsamości zarządzane zostaną automatycznie odtworzone, jeśli przenoszę subskrypcję do innego katalogu?

Nie. Jeśli przeniesiesz subskrypcję do innego katalogu, musisz ręcznie utworzyć ją ponownie i ponownie udzielić przypisań ról platformy Azure.
- W przypadku tożsamości zarządzanych przypisanych przez system: wyłącz i włącz ponownie. 
- W przypadku tożsamości zarządzanych przypisanych przez użytkownika: usuwanie, ponowne tworzenie i dołączanie ich ponownie do niezbędnych zasobów (na przykład maszyn wirtualnych)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Czy mogę użyć tożsamości zarządzanej, aby uzyskać dostęp do zasobu w innym katalogu/dzierżawie?

Nie. Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Czy istnieją limity szybkości dotyczące tożsamości zarządzanych?

Limity tożsamości zarządzanych mają zależności od limitów usługi platformy Azure, limitów usługi Azure Instance Metadata Service (IMDS) i limitów Azure Active Directory usługi.

- **Limity usług platformy Azure** definiują liczbę operacji tworzenia, które mogą być wykonywane na poziomie dzierżawy i subskrypcji. Tożsamości zarządzane przypisane przez użytkownika mają również [ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) dotyczące sposobu ich nazywania.
- **ImDS** Ogólnie rzecz biorąc, żądania do imds są ograniczone do pięciu żądań na sekundę. Żądania przekraczające ten próg zostaną odrzucone z 429 odpowiedziami. Żądania do kategorii Tożsamość zarządzana są ograniczone do 20 żądań na sekundę i 5 żądań współbieżnych. Więcej informacji można znaleźć w artykule [Azure Instance Metadata Service (Windows).](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity)
- **Azure Active Directory service** Każda tożsamość zarządzana wlicza się do limitu przydziału obiektów w dzierżawie usługi Azure AD zgodnie z opisem w tesłudze Azure AD service limits and restrictions (Limity i ograniczenia usługi Azure [AD).](../enterprise-users/directory-service-limits-restrictions.md)


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Czy można przenieść tożsamość zarządzaną przypisaną przez użytkownika do innej grupy zasobów/subskrypcji?

Przenoszenie tożsamości zarządzanej przypisanej przez użytkownika do innej grupy zasobów nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak tożsamości zarządzane działają z maszynami wirtualnymi](how-managed-identities-work-vm.md)

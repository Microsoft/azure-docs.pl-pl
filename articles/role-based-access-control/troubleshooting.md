---
title: Rozwiązywanie problemów z RBAC platformy Azure
description: Rozwiązywanie problemów z kontrolą dostępu opartą na rolach (RBAC) na platformie Azure.
services: azure-portal
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/06/2021
ms.author: rolyon
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d816854c8d8a78931060c6e56fffbaee1fde5150
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771717"
---
# <a name="troubleshoot-azure-rbac"></a>Rozwiązywanie problemów z RBAC platformy Azure

W tym artykule znajdują się odpowiedzi na niektóre często zadawane pytania dotyczące kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby wiedzieć, czego można oczekiwać podczas korzystania z ról i rozwiązywać problemy z dostępem.

## <a name="azure-role-assignments-limit"></a>Limit przypisań ról platformy Azure

Platforma Azure obsługuje maksymalnie **2000** przypisań ról na subskrypcję. Ten limit obejmuje przypisania ról w ramach subskrypcji, grupy zasobów i zakresów zasobów. Jeśli podczas próby przypisania roli zostanie wyświetlony komunikat o błędzie "Nie można utworzyć więcej przypisań ról (kod: RoleAssignmentLimitExceeded)", spróbuj zmniejszyć liczbę przypisań ról w subskrypcji.

> [!NOTE]
> Limit **przypisań ról 2000** na subskrypcję jest stały i nie można go zwiększyć.

Jeśli zbliżysz się do tego limitu, oto kilka sposobów zmniejszenia liczby przypisań ról:

- Zamiast tego dodaj użytkowników do grup i przypisz role do grup. 
- Łączenie wielu wbudowanych ról z rolą niestandardową. 
- Przypisz typowe przypisania ról w wyższym zakresie, takim jak subskrypcja lub grupa zarządzania.
- Jeśli masz już Azure AD — wersja Premium P2, przypisz przypisania ról do [Azure AD Privileged Identity Management,](../active-directory/privileged-identity-management/pim-configure.md) a nie trwale przypisane. 
- Dodaj dodatkową subskrypcję. 

Aby uzyskać liczbę przypisań ról, możesz wyświetlić wykres na stronie Kontrola dostępu [(IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) w Azure Portal. Można również użyć następujących poleceń Azure PowerShell polecenia:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemy z przypisaniami ról platformy Azure

- Jeśli nie możesz przypisać roli w aplikacji Azure Portal na kontroli dostępu **(IAM),** ponieważ opcja Dodaj przypisanie roli jest wyłączona lub ponieważ jest wyświetlany błąd uprawnień "Klient z identyfikatorem obiektu nie ma autoryzacji do wykonania   >   akcji", sprawdź, czy obecnie zalogowano użytkownika, który ma przypisaną rolę, która ma uprawnienia takie jak Właściciel lub Administrator dostępu użytkowników w zakresie, do który próbujesz przypisać `Microsoft.Authorization/roleAssignments/write` rolę. [](built-in-roles.md#owner) [](built-in-roles.md#user-access-administrator)
- Jeśli do przypisywania ról używasz jednostki usługi, może wystąpić błąd "Uprawnienia niewystarczające do ukończenia operacji". Załóżmy na przykład, że masz jednostkę usługi, która ma przypisaną rolę właściciel i próbujesz utworzyć następujące przypisanie roli jako jednostkę usługi przy użyciu interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Jeśli wystąpi błąd "Niewystarczające uprawnienia do ukończenia operacji", jest to prawdopodobnie spowodowane tym, że interfejs wiersza polecenia platformy Azure próbuje znaleźć tożsamość przypisaną w usłudze Azure AD i nie można domyślnie odczytać usługi Azure AD przez jednostkę usługi.

    Istnieją dwa sposoby rozwiązania tego błędu. Pierwszym sposobem jest przypisanie roli [Czytelnicy](../active-directory/roles/permissions-reference.md#directory-readers) katalogów do jednostki usługi, aby można było odczytać dane w katalogu.

    Drugim sposobem rozwiązania tego błędu jest utworzenie przypisania roli przy użyciu `--assignee-object-id` parametru zamiast `--assignee` . Przy użyciu `--assignee-object-id` polecenia interfejs wiersza polecenia platformy Azure pominie wyszukiwania w usłudze Azure AD. Musisz uzyskać identyfikator obiektu użytkownika, grupy lub aplikacji, do której chcesz przypisać rolę. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope)

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

- Jeśli utworzysz nową jednostkę usługi i natychmiast spróbujesz przypisać rolę do tej jednostki usługi, w niektórych przypadkach przypisanie roli może się nie powieść.

    Aby rozwiązać ten scenariusz, należy ustawić `principalType` właściwość na wartość podczas tworzenia przypisania `ServicePrincipal` roli. Należy również ustawić wartość `apiVersion` lub późniejszą wartość przypisania `2018-09-01-preview` roli. Aby uzyskać więcej informacji, zobacz [Przypisywanie](role-assignments-rest.md#new-service-principal) ról platformy Azure do nowej jednostki usługi przy użyciu interfejsu API REST lub Przypisywanie ról platformy Azure do nowej jednostki usługi przy [użyciu Azure Resource Manager szablonów](role-assignments-template.md#new-service-principal)

- Jeśli spróbujemy usunąć ostatnie przypisanie roli właściciela dla subskrypcji, może zostać wyświetlony błąd "Nie można usunąć ostatniego przypisania administratora RBAC". Usuwanie ostatniego przypisania roli Właściciela dla subskrypcji nie jest obsługiwane, aby uniknąć odseparowania subskrypcji. Jeśli chcesz anulować subskrypcję, zobacz [Anulowanie subskrypcji platformy Azure.](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="problems-with-custom-roles"></a>Problemy z rolami niestandardowymi

- Jeśli potrzebujesz kroków tworzenia roli niestandardowej, zobacz samouczki dotyczące roli niestandardowej przy użyciu interfejsu wiersza [Azure Portal](custom-roles-portal.md), [Azure PowerShell](tutorial-custom-role-powershell.md)lub interfejsu wiersza [polecenia platformy Azure.](tutorial-custom-role-cli.md)
- Jeśli nie możesz zaktualizować istniejącej roli niestandardowej, sprawdź, czy obecnie zalogowano się przy użyciu użytkownika, który ma przypisaną rolę z uprawnieniami, taką jak Właściciel lub `Microsoft.Authorization/roleDefinition/write` [Administrator dostępu użytkowników.](built-in-roles.md#user-access-administrator) [](built-in-roles.md#owner)
- Jeśli nie możesz usunąć niestandardowej roli i otrzymujesz komunikat o błędzie „Istnieją przypisania ról odwołujące się do roli (kod: RoleDefinitionHasAssignments)”, wówczas istnieją przypisania ról nadal używające roli niestandardowej. Usuń te przypisania ról i spróbuj ponownie usunąć rolę niestandardową.
- Jeśli otrzymujesz komunikat o błędzie „Przekroczono limit definicji ról. Nie można tworzyć więcej definicji ról (kod: RoleDefinitionLimitExceeded)", gdy próbujesz utworzyć nową rolę niestandardową, usuń wszystkie role niestandardowe, które nie są używane. Platforma Azure obsługuje **maksymalnie 5000 ról** niestandardowych w katalogu. (W przypadku platformy Azure (Niemcy) Azure (Chiny) — 21Vianet limit wynosi 2000 ról niestandardowych).
- Jeśli podczas próby zaktualizowania roli niestandardowej wystąpi błąd podobny do "Klient ma uprawnienia do wykonania akcji "Microsoft.Authorization/roleDefinitions/write" w zakresie "/subscriptions/{subscriptionid}", ale połączona subskrypcja nie została znaleziona" podczas próby zaktualizowania roli niestandardowej, sprawdź, czy co najmniej jeden z zakresów, które można przypisać, został usunięty w katalogu. [](role-definitions.md#assignablescopes) Jeśli zakres został usunięty, utwórz bilet pomocy technicznej, ponieważ w tej chwili nie jest dostępne żadne rozwiązanie samoobsługowe.

## <a name="custom-roles-and-management-groups"></a>Role niestandardowe i grupy zarządzania

- W roli niestandardowej można zdefiniować tylko jedną `AssignableScopes` grupę zarządzania. Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie dostępne w wersji zapoznawczej.
- Ról niestandardowych `DataActions` z programem nie można przypisać w zakresie grupy zarządzania.
- Azure Resource Manager nie weryfikuje istnienia grupy zarządzania w zakresie, który można przypisać definicji roli.
- Aby uzyskać więcej informacji na temat ról niestandardowych i grup zarządzania, zobacz [Organize your resources with Azure management groups (Organizowanie zasobów przy użyciu grup zarządzania platformy Azure).](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="transferring-a-subscription-to-a-different-directory"></a>Przenoszenie subskrypcji do innego katalogu

- Jeśli potrzebujesz kroków przenoszenia subskrypcji do innego katalogu usługi Azure AD, zobacz Transfer an Azure subscription to a different Azure AD directory (Przenoszenie subskrypcji platformy [Azure do innego katalogu usługi Azure AD).](transfer-subscription.md)
- Jeśli przenosisz subskrypcję do innego katalogu usługi Azure  AD, wszystkie przypisania ról są trwale usuwane ze źródłowego katalogu usługi Azure AD i nie są migrowane do docelowego katalogu usługi Azure AD. Należy ponownie utworzyć przypisania ról w katalogu docelowym. Musisz również ręcznie ponownie utworzyć tożsamości zarządzane dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [Często zadawane pytania i znane problemy dotyczące tożsamości zarządzanych.](../active-directory/managed-identities-azure-resources/known-issues.md)
- Jeśli jesteś administratorem globalnym usługi Azure AD i nie masz dostępu do subskrypcji po jej przeniesieniu między katalogami, [](elevate-access-global-admin.md) użyj przełącznika Zarządzanie dostępem dla zasobów platformy **Azure,** aby tymczasowo podnieść swój dostęp w celu uzyskania dostępu do subskrypcji.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemy z administratorami usług lub współadministratorami

- Jeśli masz problemy z administratorami lub administrator usługi, zobacz Dodawanie lub zmienianie administratorów subskrypcji platformy [Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) i ról klasycznego administratora subskrypcji, ról platformy Azure i [ról usługi Azure AD.](rbac-and-directory-admin-roles.md)

## <a name="access-denied-or-permission-errors"></a>Błędy odmowy dostępu lub uprawnień

- Jeśli otrzymujesz błąd przypisania „Klient z identyfikatorem obiektu nie ma autoryzacji do wykonania akcji w zakresie (kod: AuthorizationFailed)” podczas próby utworzenia zasobu, upewnij się, że obecnie logujesz się jako użytkownik mający przypisaną rolę, która ma uprawnienia zapisu do zasobu w wybranym zakresie. Na przykład do zarządzania maszynami wirtualnymi w grupie zasobów musisz mieć rolę [Współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) w grupie zasobów (lub zakresie nadrzędnym). Aby uzyskać listę uprawnień dla każdej roli wbudowanej, zobacz [Wbudowane role platformy Azure.](built-in-roles.md)
- Jeśli podczas próby utworzenia lub zaktualizowania biletu pomocy technicznej wystąpi błąd uprawnień "Nie masz uprawnień do utworzenia żądania pomocy technicznej", sprawdź, czy obecnie zalogowano się za pomocą użytkownika, który ma przypisaną rolę, taką jak Współautor żądania pomocy `Microsoft.Support/supportTickets/write` [technicznej.](built-in-roles.md#support-request-contributor)

## <a name="move-resources-with-role-assignments"></a>Przenoszenie zasobów za pomocą przypisań ról

Jeśli przeniesiesz zasób, który ma rolę platformy Azure przypisaną bezpośrednio do zasobu (lub zasobu podrzędnego), przypisanie roli nie zostanie przeniesione i zostanie oddzielone. Po zakończeniu przenoszenia należy ponownie utworzyć przypisanie roli. Po pewnym czasie oddzielone przypisanie roli zostanie automatycznie usunięte, ale najlepszym rozwiązaniem jest usunięcie przypisania roli przed przeniesieniem zasobu.

Aby uzyskać informacje na temat przenoszenia zasobów, zobacz Przenoszenie zasobów do nowej [grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="role-assignments-with-identity-not-found"></a>Nie znaleziono przypisań ról z tożsamością

Na liście przypisań ról dla usługi Azure Portal można zauważyć, że podmiot zabezpieczeń (użytkownik, grupa, nazwa  główna usługi lub tożsamość zarządzana) jest wymieniony jako Tożsamość nie znaleziono o nieznanym **typie.**

![Tożsamość nie znajduje się na liście przypisań ról platformy Azure](./media/troubleshooting/unknown-security-principal.png)

Tożsamość może nie zostać znaleziona z dwóch powodów:

- Niedawno zaprosiliśmy użytkownika podczas tworzenia przypisania roli
- Usunięto podmiot zabezpieczeń z przypisaną rolą

Jeśli niedawno zaprosiliśmy użytkownika podczas tworzenia przypisania roli, ten podmiot zabezpieczeń może nadal być w procesie replikacji w różnych regionach. Jeśli tak, zaczekaj chwilę i odśwież listę przypisań ról.

Jeśli jednak ten podmiot zabezpieczeń nie jest niedawno zaproszonym użytkownikiem, może to być usunięty podmiot zabezpieczeń. Jeśli przypiszesz rolę do podmiotu zabezpieczeń, a następnie usuniesz tę jednostkę zabezpieczeń bez wcześniejszego usunięcia przypisania roli, podmiot zabezpieczeń zostanie wymieniony jako **Nie** znaleziono tożsamości i nieznany **typ.**

Jeśli to przypisanie roli zostanie wyświetlona przy użyciu Azure PowerShell, może zostać wyświetlony pusty element z `DisplayName` `ObjectType` wartością **Nieznany.** Na przykład [get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) zwraca przypisanie roli, które jest podobne do następujących danych wyjściowych:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Podobnie po wyświetleniu listy tego przypisania roli przy użyciu interfejsu wiersza polecenia platformy Azure może zostać wyświetlony pusty element `principalName` . Na przykład [az role assignment list zwraca](/cli/azure/role/assignment#az_role_assignment_list) przypisanie roli podobne do następujących danych wyjściowych:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Pozostawienie tych przypisań ról, w których usunięto podmiot zabezpieczeń, nie jest problemem. Jeśli chcesz, możesz usunąć te przypisania ról, korzystając z kroków podobnych do innych przypisań ról. Aby uzyskać informacje na temat usuwania przypisań ról, zobacz [Usuwanie przypisań ról platformy Azure.](role-assignments-remove.md)

Jeśli w programie PowerShell spróbujesz usunąć przypisania ról przy użyciu identyfikatora obiektu i nazwy definicji roli, a więcej niż jedno przypisanie roli pasuje do Twoich parametrów, zostanie wyświetlony komunikat o błędzie: "Podane informacje nie są mapowe na przypisanie roli". Następujące dane wyjściowe pokazują przykład komunikatu o błędzie:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Jeśli zostanie wyświetlony ten komunikat o błędzie, upewnij się, że określono również `-Scope` parametry `-ResourceGroupName` lub .

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Nie są wykrywane zmiany przypisania roli

Azure Resource Manager czasami buforuje konfiguracje i dane w celu zwiększenia wydajności. Po przypisaniu ról lub usunięciu przypisań ról może upłynieć do 30 minut, aż zmiany zajdą. Jeśli używasz interfejsu wiersza Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, możesz wymusić odświeżenie zmian przypisania roli, wylogowając się i logując. Jeśli wprowadzasz zmiany przypisania roli za pomocą wywołań interfejsu API REST, możesz wymusić odświeżenie, odświeżając token dostępu.

Jeśli dodajesz lub usuwasz przypisanie roli w zakresie grupy zarządzania, a rola ma , dostęp na płaszczyźnie danych może nie być `DataActions` aktualizowany przez kilka godzin. Dotyczy to tylko zakresu grupy zarządzania i płaszczyzny danych.

## <a name="web-app-features-that-require-write-access"></a>Funkcje aplikacji internetowej, które wymagają dostępu do zapisu

Jeśli przyznasz użytkownikowi dostęp tylko do odczytu do jednej aplikacji internetowej, niektóre funkcje zostaną wyłączone, czego możesz oczekiwać. Poniższe funkcje zarządzania wymagają **dostępu do** zapisu w aplikacji internetowej (współautor lub właściciel) i nie są dostępne w żadnym scenariuszu tylko do odczytu.

* Polecenia (takie jak uruchamianie, zatrzymywanie itp.)
* Zmienianie ustawień, takich jak konfiguracja ogólna, ustawienia skalowania, ustawienia kopii zapasowej i ustawienia monitorowania
* Uzyskiwanie dostępu do poświadczeń publikowania i innych wpisów tajnych, takich jak ustawienia aplikacji i parametry połączenia
* Przesyłanie strumieniowe dzienników
* Konfiguracja dzienników zasobów
* Konsola (wiersz polecenia)
* Aktywne i ostatnie wdrożenia (dla lokalnego ciągłego wdrażania usługi Git)
* Szacowane wydatki
* Testy sieci Web
* Sieć wirtualna (widoczna dla czytnika tylko wtedy, gdy sieć wirtualna została wcześniej skonfigurowana przez użytkownika z dostępem do zapisu).

Jeśli nie możesz uzyskać dostępu do żadnego z tych kafelków, musisz poprosić administratora o dostęp współautora do aplikacji internetowej.

## <a name="web-app-resources-that-require-write-access"></a>Zasoby aplikacji internetowej, które wymagają dostępu do zapisu

Aplikacje internetowe są skomplikowane ze względu na obecność kilku różnych zasobów, które się wzajemnie ze sobą ze sobą różnią. Oto typowa grupa zasobów z kilku witrynami sieci Web:

![Grupa zasobów aplikacji internetowej](./media/troubleshooting/website-resource-model.png)

W związku z tym, jeśli udzielisz innej osobie dostępu tylko do aplikacji internetowej, większość funkcji w bloku witryny internetowej w witrynie Azure Portal wyłączona.

Te elementy wymagają **dostępu do** zapisu App Service **planu odpowiadającego** witrynie sieci Web:  

* Wyświetlanie warstwy cenowej aplikacji internetowej (Bezpłatna lub Standardowa)  
* Konfiguracja skalowania (liczba wystąpień, rozmiar maszyny wirtualnej, ustawienia skalowania automatycznego)  
* Limity przydziału (magazyn, przepustowość, procesor CPU)  

Te elementy wymagają **dostępu do** zapisu dla całej grupy **zasobów zawierającej** witrynę internetową:  

* Certyfikaty i powiązania TLS/SSL (certyfikaty TLS/SSL mogą być współdzielone między lokacjami w tej samej grupie zasobów i lokalizacji geograficznej)  
* Zasady alertów  
* Ustawienia autoskalowania  
* Składniki usługi Application Insights  
* Testy sieci Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funkcje maszyny wirtualnej, które wymagają dostępu do zapisu

Podobnie jak w przypadku aplikacji internetowych, niektóre funkcje w bloku maszyny wirtualnej wymagają dostępu do zapisu dla maszyny wirtualnej lub innych zasobów w grupie zasobów.

Maszyny wirtualne są powiązane z nazwami domen, sieciami wirtualnymi, kontami magazynu i regułami alertów.

Te elementy wymagają **dostępu do** zapisu na **maszynie wirtualnej:**

* Punkty końcowe  
* Adresy IP  
* Dyski  
* Rozszerzenia  

Wymagają one **dostępu do** zapisu zarówno dla maszyny wirtualnej **,** jak i grupy **zasobów** (wraz z nazwą domeny), w których się znajduje:  

* Zestaw dostępności  
* Zestaw o zrównoważonym obciążeniu  
* Zasady alertów  

Jeśli nie możesz uzyskać dostępu do żadnego z tych kafelków, poproś administratora o dostęp współautora do grupy zasobów.

## <a name="azure-functions-and-write-access"></a>Azure Functions i dostęp do zapisu

Niektóre funkcje programu [Azure Functions](../azure-functions/functions-overview.md) wymagają dostępu do zapisu. Jeśli na przykład użytkownik ma przypisaną rolę [Czytelnik,](built-in-roles.md#reader) nie będzie mógł wyświetlać funkcji w aplikacji funkcji. W portalu zostanie wyświetlany **komunikat (brak dostępu).**

![Aplikacje funkcji nie mają dostępu](./media/troubleshooting/functionapps-noaccess.png)

Czytelnik może kliknąć  kartę Funkcje  platformy, a następnie kliknąć pozycję Wszystkie ustawienia, aby wyświetlić niektóre ustawienia związane z aplikacją funkcji (podobnie jak w przypadku aplikacji internetowej), ale nie może modyfikować żadnego z tych ustawień. Aby uzyskać dostęp do tych funkcji, musisz mieć rolę [Współautor.](built-in-roles.md#contributor)

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów dla użytkowników-gości](role-assignments-external-users.md#troubleshoot)
- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md)
- [Wyświetlanie dzienników aktywności dla zmian kontroli RBAC platformy Azure](change-history-report.md)

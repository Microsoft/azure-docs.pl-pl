---
title: Rozwiązywanie problemów z usługą Azure RBAC
description: Rozwiązywanie problemów z kontrolą dostępu opartą na rolach (Azure RBAC).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d77468619fcd67887273b2fbd452b37add1e19b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555890"
---
# <a name="troubleshoot-azure-rbac"></a>Rozwiązywanie problemów z usługą Azure RBAC

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby wiedzieć, czego można oczekiwać przy użyciu ról i rozwiązać problemy z dostępem.

## <a name="azure-role-assignments-limit"></a>Limit przypisań ról platformy Azure

Platforma Azure obsługuje maksymalnie **2000** przypisań ról na subskrypcję. Ten limit obejmuje przypisania ról w ramach subskrypcji, grupy zasobów i zakresów zasobów. Jeśli zostanie wyświetlony komunikat o błędzie "nie można utworzyć więcej przypisań ról (kod: RoleAssignmentLimitExceeded)" podczas próby przypisania roli, spróbuj zmniejszyć liczbę przypisań ról w subskrypcji.

> [!NOTE]
> Limit przypisań ról **2000** na subskrypcję został ustalony i nie można go zwiększyć.

Jeśli zbliżasz się do tego limitu, Oto kilka sposobów zmniejszenia liczby przypisań ról:

- Dodaj użytkowników do grup i przypisz do nich role. 
- Łączenie wielu wbudowanych ról z rolą niestandardową. 
- Wykonywanie wspólnych przypisań ról w wyższym zakresie, takich jak subskrypcja lub Grupa zarządzania.
- Jeśli masz Azure AD — wersja Premium P2, ustaw przydziały ról kwalifikujące się do [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) zamiast stałego przypisywania. 
- Dodaj dodatkową subskrypcję. 

Aby uzyskać liczbę przypisań ról, wykres można wyświetlić [na stronie kontroli dostępu (IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) w Azure Portal. Można również użyć następujących Azure PowerShell poleceń:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemy z przypisaniami ról platformy Azure

- Jeśli nie można przypisać roli w Azure Portal na **kontroli dostępu (IAM)** , ponieważ opcja **Dodaj**  >  **przypisanie roli** jest wyłączona lub z powodu błędu uprawnień "klient z identyfikatorem obiektu nie ma autoryzacji do wykonania akcji", sprawdź, czy zalogowano się przy użyciu użytkownika, do którego przypisano rolę z `Microsoft.Authorization/roleAssignments/write` uprawnieniem, takim jak [właściciel](built-in-roles.md#owner) lub [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) w zakresie, w którym próbujesz przypisać rolę.
- Jeśli używasz jednostki usługi do przypisywania ról, może zostać wyświetlony komunikat o błędzie "niewystarczające uprawnienia do ukończenia tej operacji". Załóżmy na przykład, że masz nazwę główną usługi, która ma przypisaną rolę właściciela, i spróbuj utworzyć następujące przypisanie roli jako nazwę główną usługi przy użyciu interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Jeśli zostanie wyświetlony błąd "niewystarczające uprawnienia do ukończenia operacji", prawdopodobnie jest to spowodowane tym, że interfejs wiersza polecenia platformy Azure próbuje wyszukać tożsamość osoby przydzielonej w usłudze Azure AD, a jednostka usługi nie może domyślnie odczytać usługi Azure AD.

    Istnieją dwa sposoby, aby potencjalnie rozwiązać ten problem. Pierwszy sposób polega na przypisaniu roli [czytniki katalogów](../active-directory/roles/permissions-reference.md#directory-readers) do nazwy głównej usługi, aby mogła ona odczytywać dane w katalogu.

    Drugim sposobem na rozwiązanie tego błędu jest utworzenie przypisania roli przy użyciu `--assignee-object-id` parametru zamiast `--assignee` . Za pomocą `--assignee-object-id` interfejsu wiersza polecenia platformy Azure pominie wyszukiwanie w usłudze Azure AD. Konieczne będzie uzyskanie identyfikatora obiektu użytkownika, grupy lub aplikacji, do której ma zostać przypisana rola. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope).

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```
- Jeśli spróbujesz usunąć przypisanie ostatniego właściciela roli dla subskrypcji, może zostać wyświetlony komunikat o błędzie "nie można usunąć ostatniego przypisania administratora RBAC". Usuwanie przypisania roli ostatniego właściciela subskrypcji nie jest obsługiwane, aby uniknąć oddzielenia subskrypcji. Jeśli chcesz anulować subskrypcję, zobacz sekcję [Anulowanie subskrypcji platformy Azure](../cost-management-billing/manage/cancel-azure-subscription.md).

## <a name="problems-with-custom-roles"></a>Problemy z rolami niestandardowymi

- Aby dowiedzieć się, jak utworzyć rolę niestandardową, zapoznaj się z samouczkami ról niestandardowych przy użyciu [Azure Portal](custom-roles-portal.md) (obecnie w wersji zapoznawczej), [Azure PowerShell](tutorial-custom-role-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md).
- Jeśli nie można zaktualizować istniejącej roli niestandardowej, należy sprawdzić, czy użytkownik jest zalogowany przy użyciu przypisanej do roli `Microsoft.Authorization/roleDefinition/write` uprawnienia, takiej jak [właściciel](built-in-roles.md#owner) lub [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator).
- Jeśli nie możesz usunąć niestandardowej roli i otrzymujesz komunikat o błędzie „Istnieją przypisania ról odwołujące się do roli (kod: RoleDefinitionHasAssignments)”, wówczas istnieją przypisania ról nadal używające roli niestandardowej. Usuń te przypisania ról i spróbuj ponownie usunąć rolę niestandardową.
- Jeśli otrzymujesz komunikat o błędzie „Przekroczono limit definicji ról. Nie można utworzyć więcej definicji ról (kod: RoleDefinitionLimitExceeded) "podczas próby utworzenia nowej roli niestandardowej, Usuń wszystkie niestandardowe role, które nie są używane. Platforma Azure obsługuje do **5000** ról niestandardowych w katalogu. (W przypadku platformy Azure (Niemcy i Azure Chiny 21Vianet limit wynosi 2000 ról niestandardowych).
- Jeśli zostanie wyświetlony komunikat o błędzie podobny do "klient ma uprawnienia do wykonania akcji" Microsoft. Authorization/roleDefinitions/Write "w zakresie"/subscriptions/{SubscriptionID} ", ale nie znaleziono połączonej subskrypcji" podczas próby zaktualizowania roli niestandardowej, sprawdź, czy w katalogu usunięto co najmniej jeden z [przypisanych zakresów](role-definitions.md#assignablescopes) . Jeśli zakres został usunięty, utwórz bilet pomocy technicznej, ponieważ w tej chwili nie jest dostępne żadne rozwiązanie samoobsługowe.

## <a name="custom-roles-and-management-groups"></a>Role niestandardowe i grupy zarządzania

- W roli niestandardowej można zdefiniować tylko jedną grupę zarządzania `AssignableScopes` . Dodawanie grupy zarządzania do programu `AssignableScopes` jest obecnie w wersji zapoznawczej.
- Ról niestandardowych z `DataActions` nie można przypisać do zakresu grupy zarządzania.
- Azure Resource Manager nie sprawdza poprawności istnienia grupy zarządzania w zakresie możliwym do przypisania definicji roli.
- Aby uzyskać więcej informacji na temat ról niestandardowych i grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Transferowanie subskrypcji do innego katalogu

- Jeśli potrzebujesz kroków dotyczących sposobu transferowania subskrypcji do innego katalogu usługi Azure AD, zobacz [transfer subskrypcji platformy Azure do innego katalogu usługi Azure AD](transfer-subscription.md).
- Jeśli przeniesiesz subskrypcję do innego katalogu usługi Azure AD, wszystkie przypisania ról zostaną **trwale** usunięte ze źródłowego katalogu usługi Azure AD i nie zostaną zmigrowane do docelowego katalogu usługi Azure AD. Musisz ponownie utworzyć przypisania roli w katalogu docelowym. Należy również ręcznie odtworzyć zarządzane tożsamości dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [często zadawane pytania i znane problemy związane z tożsamościami zarządzanymi](../active-directory/managed-identities-azure-resources/known-issues.md).
- Jeśli jesteś administratorem globalnym usługi Azure AD i nie masz dostępu do subskrypcji po przeniesieniu między katalogami, użyj przełącznika **Zarządzanie dostępem do zasobów platformy Azure** , aby tymczasowo [podnieść poziom dostępu](elevate-access-global-admin.md) w celu uzyskania dostępu do subskrypcji.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemy z administratorami usług lub współadministratorami

- Jeśli masz problemy z administratorem usługi lub współadministratorem, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) i [ról administratora klasycznej subskrypcji, ról platformy Azure i ról usługi Azure AD](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Odmowa dostępu lub błędy uprawnień

- Jeśli otrzymujesz błąd przypisania „Klient z identyfikatorem obiektu nie ma autoryzacji do wykonania akcji w zakresie (kod: AuthorizationFailed)” podczas próby utworzenia zasobu, upewnij się, że obecnie logujesz się jako użytkownik mający przypisaną rolę, która ma uprawnienia zapisu do zasobu w wybranym zakresie. Na przykład do zarządzania maszynami wirtualnymi w grupie zasobów musisz mieć rolę [Współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) w grupie zasobów (lub zakresie nadrzędnym). Listę uprawnień dla każdej roli wbudowanej można znaleźć [w temacie Role wbudowane platformy Azure](built-in-roles.md).
- Jeśli zostanie wyświetlony komunikat o błędzie "nie masz uprawnień do utworzenia żądania pomocy technicznej" podczas próby utworzenia lub zaktualizowania biletu pomocy technicznej, sprawdź, czy jesteś zalogowanym użytkownikiem, do którego przypisano rolę z `Microsoft.Support/supportTickets/write` uprawnieniem, takim jak [współautor żądania pomocy technicznej](built-in-roles.md#support-request-contributor).

## <a name="move-resources-with-role-assignments"></a>Przenoszenie zasobów z przypisaniami ról

W przypadku przeniesienia zasobu z rolą platformy Azure przypisaną bezpośrednio do zasobu (lub zasobu podrzędnego) przypisanie roli nie zostanie przeniesione i zostanie oddzielone. Po przeniesieniu należy ponownie utworzyć przypisanie roli. Ostatecznie przypisanie oddzielonej roli zostanie automatycznie usunięte, ale najlepszym rozwiązaniem jest usunięcie przypisania roli przed przeniesieniem zasobu.

Informacje o sposobie przenoszenia zasobów znajdują się w temacie [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="role-assignments-with-identity-not-found"></a>Nie znaleziono przypisań ról o tożsamości

Na liście przypisań ról dla Azure Portal można zauważyć, że podmiot zabezpieczeń (użytkownika, Grupa, nazwa główna usługi lub tożsamość zarządzana) jest wymieniony jako **tożsamość nie została znaleziona** z **nieznanym** typem.

![Nie znaleziono tożsamości wymienionej w przypisaniach ról platformy Azure](./media/troubleshooting/unknown-security-principal.png)

Tożsamość może nie zostać znaleziona z dwóch powodów:

- Ostatnio zaproszono użytkownika podczas tworzenia przypisania roli
- Usunięto podmiot zabezpieczeń, który miał przypisanie roli

Jeśli użytkownik ostatnio zaprosił użytkownika podczas tworzenia przypisania roli, ten podmiot zabezpieczeń może nadal znajdować się w procesie replikacji między regionami. Jeśli tak, poczekaj chwilę i Odśwież listę przypisań ról.

Jeśli jednak ten podmiot zabezpieczeń nie jest ostatnio zaproszonym użytkownikiem, może być usuniętym podmiotem zabezpieczeń. Jeśli przypiszesz rolę do podmiotu zabezpieczeń, a następnie usuniesz ten podmiot zabezpieczeń bez wcześniejszego usunięcia przypisania roli, podmiot zabezpieczeń będzie wymieniony jako **tożsamość nie zostanie znaleziona** i **nieznany** typ.

Jeśli lista tego przypisania roli zostanie wyświetlona przy użyciu Azure PowerShell, może być widoczny pusty `DisplayName` i `ObjectType` ustawiony jako **nieznany**. Na przykład polecenie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) zwraca przypisanie roli podobne do następujących danych wyjściowych:

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

Podobnie jeśli zostanie wyświetlone to przypisanie roli przy użyciu interfejsu wiersza polecenia platformy Azure, może zostać wyświetlony pusty `principalName` . Na przykład [AZ role list przypisanie](/cli/azure/role/assignment#az-role-assignment-list) zwraca przypisanie roli, które jest podobne do następujących danych wyjściowych:

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

Nie jest to problem, aby opuścić te przypisania ról, w których podmiot zabezpieczeń został usunięty. Jeśli chcesz, możesz usunąć te przypisania ról, korzystając z kroków, które są podobne do innych przypisań ról. Aby uzyskać informacje o sposobach usuwania przypisań ról, zobacz [usuwanie przypisań ról platformy Azure](role-assignments-remove.md).

W programie PowerShell, jeśli spróbujesz usunąć przypisania roli przy użyciu identyfikatora obiektu i nazwy definicji roli, a więcej niż jedno przypisanie roli jest zgodne z parametrami, zostanie wyświetlony komunikat o błędzie: "podane informacje nie są mapowane na przypisanie roli". Poniższe dane wyjściowe pokazują przykład komunikatu o błędzie:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Jeśli ten komunikat o błędzie jest wyświetlany, upewnij się, że określono `-Scope` również `-ResourceGroupName` parametry lub.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Zmiany przypisania roli nie są wykrywane

Czasami Azure Resource Manager buforuje konfiguracje i dane w celu zwiększenia wydajności. Po przypisaniu ról lub usunięciu przypisań ról może upłynąć do 30 minut, aby zmiany zaczęły obowiązywać. Jeśli używasz Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, możesz wymusić odświeżenie zmian przypisania roli przez wylogowanie się i zalogowanie się. W przypadku wprowadzania zmian przypisywania ról przy użyciu wywołań interfejsu API REST można wymusić odświeżenie tokenu dostępu.

Jeśli dodajesz lub usuniesz przypisanie roli w zakresie grupy zarządzania i rola ma `DataActions` , dostęp na płaszczyźnie danych może nie być aktualizowany przez kilka godzin. Dotyczy to tylko zakresu grupy zarządzania i płaszczyzny danych.

## <a name="web-app-features-that-require-write-access"></a>Funkcje aplikacji sieci Web, które wymagają dostępu do zapisu

Jeśli przyznasz użytkownikowi dostęp tylko do odczytu do pojedynczej aplikacji sieci Web, niektóre funkcje są wyłączone, które mogą być nieoczekiwane. Następujące funkcje zarządzania wymagają dostępu do **zapisu** do aplikacji sieci Web (współautor lub właściciela) i nie są dostępne w żadnym scenariuszu tylko do odczytu.

* Polecenia (takie jak uruchamianie, zatrzymywanie itp.)
* Zmiana ustawień, takich jak Konfiguracja ogólna, ustawienia skalowania, ustawienia kopii zapasowej i ustawienia monitorowania
* Uzyskiwanie dostępu do poświadczeń publikowania i innych wpisów tajnych, takich jak ustawienia aplikacji i parametry połączenia
* Przesyłanie strumieniowe dzienników
* Konfiguracja dzienników zasobów
* Konsola (wiersz polecenia)
* Aktywne i niedawne wdrożenia (dla lokalnego wdrożenia usługi Git)
* Szacowane wydatki
* Testy sieci Web
* Sieć wirtualna (widoczna tylko dla czytnika, jeśli sieć wirtualna została wcześniej skonfigurowana przez użytkownika z prawem do zapisu).

Jeśli nie możesz uzyskać dostępu do żadnego z tych kafelków, musisz polecić administratorowi dostęp współautora do aplikacji sieci Web.

## <a name="web-app-resources-that-require-write-access"></a>Zasoby aplikacji sieci Web, które wymagają dostępu do zapisu

Aplikacje sieci Web są skomplikowane przez obecność kilku różnych zasobów, które współpraca. Poniżej przedstawiono typową grupę zasobów z kilkoma witrynami sieci Web:

![Grupa zasobów aplikacji sieci Web](./media/troubleshooting/website-resource-model.png)

W związku z tym, jeśli przyznasz komuś dostęp tylko do aplikacji sieci Web, większość funkcji w bloku witryna sieci Web w Azure Portal jest wyłączona.

Elementy te wymagają dostępu do **zapisu** do **planu App Service** , który odpowiada witrynie sieci Web:  

* Wyświetlanie warstwy cenowej aplikacji sieci Web (bezpłatna lub standardowa)  
* Konfiguracja skalowania (liczba wystąpień, rozmiar maszyny wirtualnej, ustawienia skalowania automatycznego)  
* Przydziały (magazyn, przepustowość, procesor CPU)  

Te elementy wymagają dostępu do **zapisu** w całej **grupie zasobów** zawierającej witrynę sieci Web:  

* Certyfikaty i powiązania protokołu TLS/SSL (certyfikaty TLS/SSL mogą być współużytkowane między lokacjami w tej samej grupie zasobów i lokalizacji geograficznej)  
* Zasady alertów  
* Ustawienia skalowania automatycznego  
* Składniki usługi Application Insights  
* Testy sieci Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funkcje maszyny wirtualnej, które wymagają dostępu do zapisu

Podobnie jak w przypadku aplikacji sieci Web, niektóre funkcje w bloku maszyny wirtualnej wymagają dostępu do zapisu do maszyny wirtualnej lub do innych zasobów w grupie zasobów.

Maszyny wirtualne są powiązane z nazwami domen, sieciami wirtualnymi, kontami magazynu i regułami alertów.

Elementy te wymagają dostępu do **zapisu** dla **maszyny wirtualnej**:

* Punkty końcowe  
* Adresy IP  
* Dyski  
* Rozszerzenia  

Wymagają one dostępu do **zapisu** zarówno dla **maszyny wirtualnej**, jak i **grupy zasobów** (wraz z nazwą domeny), w której znajduje się:  

* Zestaw dostępności  
* Zestaw o zrównoważonym obciążeniu  
* Zasady alertów  

Jeśli nie możesz uzyskać dostępu do żadnego z tych kafelków, poprosimy administratora o dostęp współautora do grupy zasobów.

## <a name="azure-functions-and-write-access"></a>Dostęp Azure Functions i zapisu

Niektóre funkcje [Azure Functions](../azure-functions/functions-overview.md) wymagają dostępu do zapisu. Jeśli na przykład użytkownik ma przypisaną rolę [czytelnik](built-in-roles.md#reader) , nie będzie mógł wyświetlać funkcji w ramach aplikacji funkcji. Zostanie wyświetlony portal **(brak dostępu)**.

![Aplikacje funkcji Brak dostępu](./media/troubleshooting/functionapps-noaccess.png)

Czytelnik może kliknąć kartę **funkcje platformy** , a następnie kliknąć pozycję **wszystkie ustawienia** , aby wyświetlić niektóre ustawienia związane z aplikacją funkcji (podobną do aplikacji sieci Web), ale nie mogą modyfikować żadnego z tych ustawień. Aby uzyskać dostęp do tych funkcji, będzie potrzebna rola [współautor](built-in-roles.md#contributor) .

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z użytkownikami-Gośćmi](role-assignments-external-users.md#troubleshoot)
- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md)
- [Wyświetlanie dzienników aktywności dla zmian RBAC platformy Azure](change-history-report.md)

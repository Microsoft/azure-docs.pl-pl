---
title: Rozwiązywanie typowych błędów związanych z wdrażaniem
description: Opisuje sposób rozwiązywania typowych błędów podczas wdrażania zasobów na platformie Azure przy użyciu Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.openlocfilehash: 07c197f1b54522b96a3bfa2d6a5ce7b368be3b35
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789181"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Usuwanie typowych błędów wdrożeń na platformie Azure przy użyciu usługi Azure Resource Manager

W tym artykule opisano niektóre typowe błędy wdrażania platformy Azure oraz przedstawiono informacje na temat ich rozwiązywania. Jeśli nie możesz znaleźć kodu błędu wdrażania, zobacz [Znajdowanie kodu błędu](#find-error-code).

Jeśli szukasz informacji o kodzie błędu i te informacje nie są podane w tym artykule, daj nam znać. W dolnej części tej strony możesz dodać opinię. Opinie są śledzone w przypadku problemów z serwisem GitHub.

## <a name="error-codes"></a>Kody błędów

| Kod błędu | Ograniczanie ryzyka | Więcej informacji |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Postępuj zgodnie z ograniczeniami nazewnictwa dla kont magazynu. | [Rozwiązywanie problemów z nazwą konta magazynu](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Sprawdź dostępne właściwości konta magazynu. | [konta magazynu](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Klaster lub region nie ma dostępnych zasobów lub nie może obsługiwać żądanego rozmiaru maszyny wirtualnej. Ponów żądanie w późniejszym czasie lub zażądaj innego rozmiaru maszyny wirtualnej. | [Problemy z aprowizowaniem i alokacją](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux)w systemie Linux, problemy [z aprowizowaniem i alokacją w systemie Windows](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) oraz rozwiązywanie problemów z [alokacją](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | Poczekaj na zakończenie operacji współbieżnych. | |
| AuthorizationFailed | Twoje konto lub główna usługa nie ma wystarczającego dostępu do ukończenia wdrożenia. Sprawdź rolę, do której należy Twoje konto, oraz jej dostęp do zakresu wdrożenia.<br><br>Ten błąd może wystąpić, gdy wymagany dostawca zasobów nie jest zarejestrowany. | [Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| BadRequest | Wysłano wartości wdrożenia, które nie są zgodne z oczekiwaniami Resource Manager. Sprawdź wewnętrzny komunikat o stanie, aby uzyskać pomoc w rozwiązywaniu problemów. | [Informacje o szablonie](/azure/templates/) [i obsługiwane lokalizacje](resource-location.md) |
| Konflikt | Żądasz operacji, która nie jest dozwolona w bieżącym stanie zasobu. Na przykład zmiana rozmiaru dysku jest dozwolona tylko podczas tworzenia maszyny wirtualnej lub gdy przydział maszyny wirtualnej jest cofany. | |
| DeploymentActiveAndUneditable | Poczekaj na zakończenie współbieżnego wdrażania w tej grupie zasobów. | |
| DeploymentFailedCleanUp | Po wdrożeniu w trybie ukończenia wszystkie zasoby, które nie znajdują się w szablonie, zostaną usunięte. Ten błąd występuje, gdy nie masz odpowiednich uprawnień do usuwania wszystkich zasobów, które nie są w szablonie. Aby uniknąć błędu, zmień tryb wdrażania na przyrostowy. | [Tryby wdrażania usługi Azure Resource Manager](deployment-modes.md) |
| DeploymentNameInvalidCharacters | Nazwa wdrożenia może zawierać tylko literę, cyfrę, "-", "." lub "_". | |
| DeploymentNameLengthLimitExceeded | Nazwy wdrożeń są ograniczone do 64 znaków.  | |
| DeploymentFailed | Błąd DeploymentFailed jest ogólnym błędem, który nie zawiera szczegółowych informacji potrzebnych do rozwiązania błędu. Poszukaj w szczegółach błędu kodu błędu, który zawiera więcej informacji. | [Znajdowanie kodu błędu](#find-error-code) |
| DeploymentQuotaExceeded | Jeśli osiągniesz limit 800 wdrożeń na grupę zasobów, usuń wdrożenia z historii, które nie są już potrzebne. | [Rozwiązywanie błędu, gdy liczba wdrożeń przekracza 800](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | Uproszczenie szablonu w celu zmniejszenia rozmiaru. | [Rozwiązywanie błędów rozmiaru szablonu](error-job-size-exceeded.md) |
| DnsRecordInUse | Nazwa rekordu DNS musi być unikatowa. Wprowadź inną nazwę. | |
| ImageNotFound | Sprawdź ustawienia obrazu maszyny wirtualnej. |  |
| InUseSubnetCannotBeDeleted | Ten błąd może wystąpić podczas próby zaktualizowania zasobu, a żądanie jest przetwarzane przez usunięcie i utworzenie zasobu. Pamiętaj, aby określić wszystkie niezmienione wartości. | [Aktualizowanie zasobu](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Pobierz token dostępu dla odpowiedniej dzierżawy. Token można uzyskać tylko z dzierżawy, do której należy Twoje konto. | |
| InvalidContentLink | Najprawdopodobniej podjęto próbę połączenia z zagnieżdżony szablon, który jest niedostępny. Sprawdź dwukrotnie podany dla zagnieżdżony szablon URI. Jeśli szablon istnieje na koncie magazynu, upewnij się, że jest dostępny. Może być konieczne podanie tokenu SAS. Obecnie nie można utworzyć linku do szablonu, który znajduje się na koncie magazynu za zaporą [usługi Azure Storage.](../../storage/common/storage-network-security.md) Rozważ przeniesienie szablonu do innego repozytorium, takiego jak GitHub. | [Połączone szablony](linked-templates.md) |
| InvalidDeploymentLocation | Podczas wdrażania na poziomie subskrypcji podano inną lokalizację dla wcześniej używanej nazwy wdrożenia. | [Wdrożenia na poziomie subskrypcji](deploy-to-subscription.md) |
| InvalidParameter | Jedna z wartości podanych dla zasobu nie odpowiada oczekiwanej wartości. Ten błąd może wynikać z wielu różnych warunków. Na przykład hasło może być niewystarczające lub nazwa obiektu blob może być niepoprawna. Komunikat o błędzie powinien wskazywać, którą wartość należy poprawić. | |
| InvalidRequestContent | Wartości wdrożenia obejmują wartości, które nie są rozpoznawane lub brakuje wymaganych wartości. Potwierdź wartości dla typu zasobu. | [Dokumentacja dotycząca szablonów](/azure/templates/) |
| InvalidRequestFormat | Włącz rejestrowanie debugowania podczas uruchamiania wdrożenia i sprawdź zawartość żądania. | [Rejestrowanie debugowania](#enable-debug-logging) |
| InvalidResourceNamespace | Sprawdź przestrzeń nazw zasobów określoną we **właściwości** type. | [Dokumentacja dotycząca szablonów](/azure/templates/) |
| InvalidResourceReference | Zasób jeszcze nie istnieje lub jest nieprawidłowo przywołyny. Sprawdź, czy musisz dodać zależność. Sprawdź, czy użycie **funkcji** odwołania zawiera wymagane parametry dla scenariusza. | [Rozwiązywanie zależności](error-not-found.md) |
| InvalidResourceType | Sprawdź typ zasobu określony we **właściwości** type. | [Dokumentacja dotycząca szablonów](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Zarejestruj swoją subskrypcję u dostawcy zasobów. | [Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| InvalidTemplate | Sprawdź składnię szablonu, aby uzyskać informacje o błędach. | [Rozwiązywanie problemów z nieprawidłowym szablonem](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Usuń niepotrzebne zależności. | [Rozwiązywanie zależności cyklicznych](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Uproszczenie szablonu w celu zmniejszenia rozmiaru. | [Rozwiązywanie błędów rozmiaru szablonu](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Sprawdź, czy Twoje konto należy do tej samej dzierżawy co grupa zasobów, w której wdrażasz. | |
| LinkedInvalidPropertyId | Identyfikator zasobu dla zasobu nie jest poprawnie rozpoznany. Sprawdź, czy po podaj wszystkie wymagane wartości identyfikatora zasobu, w tym identyfikator subskrypcji, nazwę grupy zasobów, typ zasobu, nazwę zasobu nadrzędnego (w razie potrzeby) i nazwę zasobu. | |
| LocationRequired | Podaj lokalizację zasobu. | [Ustawianie lokalizacji](resource-location.md) |
| NiezgodnośćResourceSegments | Upewnij się, że zasób zagnieżdżony ma poprawną liczbę segmentów w nazwie i typie. | [Rozwiązywanie segmentów zasobów](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Sprawdź stan rejestracji dostawcy zasobów i obsługiwane lokalizacje. | [Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Zarejestruj swoją subskrypcję u dostawcy zasobów. | [Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Sprawdź stan rejestracji dostawcy zasobów. | [Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| NotFound | Być może próbujesz wdrożyć zasób zależny równolegle z zasobem nadrzędnym. Sprawdź, czy musisz dodać zależność. | [Rozwiązywanie zależności](error-not-found.md) |
| OperationNotAllowed | Wdrożenie próbuje operację, która przekracza limit przydziału subskrypcji, grupy zasobów lub regionu. Jeśli to możliwe, popraw wdrożenie, aby zachować limity przydziału. W przeciwnym razie rozważ zażądanie zmiany limitów przydziału. | [Rozwiązywanie problemów z limitami przydziału](error-resource-quota.md) |
| ParentResourceNotFound | Przed utworzeniem zasobów nadrzędnych upewnij się, że istnieje zasób nadrzędny. | [Rozwiązywanie problemu z zasobem nadrzędnym](error-parent-resource.md) |
| PasswordTooLong | Być może wybrano hasło ze zbyt wieloma znakami lub przekonwertowano wartość hasła na bezpieczny ciąg przed przekazaniem go jako parametru. Jeśli szablon zawiera parametr **ciągu bezpiecznego,** nie trzeba konwertować wartości na bezpieczny ciąg. Podaj wartość hasła jako tekst. |  |
| PrivateIPAddressInReservedRange | Określony adres IP zawiera zakres adresów wymagany przez platformę Azure. Zmień adres IP, aby uniknąć zarezerwowanego zakresu. | [Adresy IP](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | Określony adres IP znajduje się poza zakresem podsieci. Zmień adres IP na należący do zakresu podsieci. | [Adresy IP](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Niektórych właściwości wdrożonego zasobu nie można zmienić. Podczas aktualizowania zasobu ogranicz zmiany do dozwolonych właściwości. | [Aktualizowanie zasobu](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | Twoja subskrypcja zawiera zasady zasobów, które uniemożliwiają wykonanie akcji podczas wdrażania. Znajdź zasady, które blokową akcję. Jeśli to możliwe, zmień wdrożenie, aby spełnić ograniczenia zasad. | [Rozwiązywanie zasad](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Podaj nazwę zasobu, która nie zawiera nazwy zarezerwowanej. | [Nazwy zasobów zarezerwowanych](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Poczekaj na zakończenie usuwania. | |
| ResourceGroupNotFound | Sprawdź nazwę docelowej grupy zasobów dla wdrożenia. Docelowa grupa zasobów musi już istnieć w Subskrypcji. Sprawdź kontekst subskrypcji. | [Interfejs wiersza polecenia platformy Azure](/cli/azure/account?#az_account_set) w [programie PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Wdrożenie odwołuje się do zasobu, który nie może zostać rozpoznany. Sprawdź, czy użycie funkcji **referencyjnej** zawiera parametry wymagane dla scenariusza. | [Rozwiązywanie odwołań](error-not-found.md) |
| ResourceQuotaExceeded | Wdrożenie próbuje utworzyć zasoby, które przekraczają limit przydziału dla subskrypcji, grupy zasobów lub regionu. Jeśli to możliwe, popraw infrastrukturę, aby nie przekroczono limitów przydziału. W przeciwnym razie rozważ zażądanie zmiany limitów przydziału. | [Rozwiązywanie limitów przydziału](error-resource-quota.md) |
| SkuNotAvailable | Wybierz pozycję SKU (na przykład rozmiar maszyny wirtualnej), która jest dostępna dla wybranej lokalizacji. | [Rozwiązywanie problemów z sku](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Podaj unikatową nazwę konta magazynu. | [Rozwiązywanie problemów z nazwą konta magazynu](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Podaj unikatową nazwę konta magazynu. | [Rozwiązywanie problemów z nazwą konta magazynu](error-storage-account-name.md) |
| StorageAccountNotFound | Sprawdź subskrypcję, grupę zasobów i nazwę konta magazynu, które chcesz użyć. | |
| PodsieciNotInSameVnet | Maszyna wirtualna może mieć tylko jedną sieć wirtualną. Podczas wdrażania kilku kart sieciowych upewnij się, że należą one do tej samej sieci wirtualnej. | [Wiele karty sieciowe](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Nie można uzyskać dostępu do określonej subskrypcji wdrożenia. Może to być nieprawidłowy identyfikator subskrypcji, użytkownik wdrażający szablon nie ma odpowiednich uprawnień do wdrożenia w subskrypcji lub identyfikator subskrypcji ma nieprawidłowy format. W przypadku wdrażania [](./deploy-to-resource-group.md)w zakresach za pomocą wdrożeń zagnieżdżonych podaj identyfikator GUID subskrypcji. | |
| SubscriptionNotRegistered | Podczas wdrażania zasobu dostawca zasobów musi być zarejestrowany dla subskrypcji. Jeśli używasz szablonu Azure Resource Manager do wdrożenia, dostawca zasobów jest automatycznie rejestrowany w subskrypcji. Czasami automatyczna rejestracja nie jest ukończona w czasie. Aby uniknąć tego sporadycznie występującego błędu, zarejestruj dostawcę zasobów przed wdrożeniem. | [Rozwiązywanie problemów z rejestracją](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Usuń niepotrzebne zależności. | [Rozwiązywanie zależności cyklicznych](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Zmniejsz liczbę grup zasobów dla pojedynczego wdrożenia. | [Wdrażanie w zakresie między zakresami](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>Znajdowanie kodu błędu

Istnieją dwa typy błędów, które mogą wystąpić:

* błędy weryfikacji
* błędy wdrażania

Błędy weryfikacji wynikają z sytuacji, które można rozpoznać przed przystąpieniem do wdrożenia. Są to na przykład błędy składniowe w szablonie lub próby wdrożenia zasobów, które przekraczają limity przydziału w ramach subskrypcji. Błędy wdrażania wynikają z warunków, które mają miejsce podczas procesu wdrażania. Obejmują one próby uzyskania dostępu do zasobu, który jest wdrażany równolegle.

Oba rodzaje błędów zwracają kod błędu, którego należy użyć do rozwiązania problemów z wdrożeniem. Oba rodzaje błędów są wyświetlane w [dzienniku aktywności](../management/view-activity-logs.md). Błędy weryfikacji nie są jednak wyświetlane w historii wdrażania, ponieważ wdrożenie nie jest w takim przypadku rozpoczynane.

### <a name="validation-errors"></a>Błędy walidacji

Podczas wdrażania za pośrednictwem portalu zobaczysz błąd weryfikacji po przesłaniu własnych wartości.

![wyświetlanie błędu weryfikacji portalu](./media/common-deployment-errors/validation-error.png)

Wybierz komunikat, aby uzyskać więcej informacji. Na poniższej ilustracji zobaczysz błąd **InvalidTemplateDeployment** i komunikat informujący o zablokowaniu wdrożenia przez zasady.

![wyświetlanie szczegółów walidacji](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Błędy wdrażania

Jeśli operacja przeszła weryfikację, ale kończy się niepowodzeniem podczas wdrażania, otrzymasz błąd wdrażania.

Aby wyświetlić kody błędów wdrażania i komunikaty za pomocą programu PowerShell, użyj następującego polecenia:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Aby wyświetlić kody błędów wdrażania i komunikaty za pomocą wiersza polecenia platformy Azure, użyj następującego polecenia:

```azurecli-interactive
az deployment operation group list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

W portalu wybierz powiadomienie.

![błąd powiadomienia](./media/common-deployment-errors/notification.png)

Zostanie wyświetlonych więcej szczegółów dotyczących wdrożenia. Wybierz opcję, aby uzyskać więcej informacji o błędzie.

![wdrożenie nie powiodło się](./media/common-deployment-errors/deployment-failed.png)

Zostanie wyświetlony komunikat o błędzie i kody błędu. Zauważ, że są tam podane dwa kody błędu. Pierwszy kod błędu (**DeploymentFailed**) identyfikuje błąd ogólny, który nie zapewnia szczegółów niezbędnych do rozwiązania problemu. Drugi kod błędu (**StorageAccountNotFound**) udostępnia szczegółowe informacje, których potrzebujesz.

![szczegóły błędu](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Włączanie rejestrowania debugowania

Czasami potrzebujesz więcej informacji na temat żądania i odpowiedzi, aby dowiedzieć się, co poszło nie tak. Podczas wdrażania można zażądać, aby dodatkowe informacje zostały zarejestrowane podczas wdrażania.

### <a name="powershell"></a>PowerShell

W programie PowerShell ustaw parametr **DeploymentDebugLogLevel** na wartość All, ResponseContent lub RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Sprawdź zawartość żądania za pomocą następującego polecenia cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Lub zawartość odpowiedzi z:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Te informacje mogą pomóc w ustaleniu, czy wartość w szablonie jest niepoprawnie ustawiona.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Obecnie interfejs wiersza polecenia platformy Azure nie obsługuje włączania rejestrowania debugowania, ale można pobrać rejestrowanie debugowania.

Sprawdź operacje wdrażania za pomocą następującego polecenia:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Sprawdź zawartość żądania za pomocą następującego polecenia:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Sprawdź zawartość odpowiedzi za pomocą następującego polecenia:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Szablon zagnieżdżony

Aby rejestrować informacje debugowania dla zagnieżdżonych szablonów, użyj **debugSetting** elementu.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>Tworzenie szablonu rozwiązywania problemów

W niektórych przypadkach najprostszym sposobem rozwiązywania problemów z szablonem jest przetestowanie jego części. Możesz utworzyć uproszczony szablon, który pozwala skupić się na części, która jest uważana za powodującą błąd. Załóżmy na przykład, że występuje błąd podczas odwoływania się do zasobu. Zamiast zajmować się całym szablonem, utwórz szablon, który zwraca część, która może powodować problem. Może pomóc w ustaleniu, czy przekazywanie odpowiednich parametrów, poprawne używanie funkcji szablonu i pobieranie spodziewanych zasobów.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

Załóżmy też, że występują błędy wdrażania, które są związane z niepoprawnym ustawianiem zależności. Przetestuj szablon, rozbijając go na uproszczone szablony. Najpierw utwórz szablon, który wdraża tylko jeden zasób (taki jak SQL Server). Jeśli masz pewność, że ten zasób został prawidłowo zdefiniowany, dodaj zasób, który od niego zależy (na przykład SQL Database). Jeśli te dwa zasoby są prawidłowo zdefiniowane, dodaj inne zasoby zależne (na przykład zasady inspekcji). Między każdym wdrożeniem testowym usuń grupę zasobów, aby upewnić się, że odpowiednio przetestowasz zależności.

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek rozwiązywania problemów, zobacz [Samouczek: rozwiązywanie](template-tutorial-troubleshoot.md) problemów Resource Manager wdrożeniami szablonów
* Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Audit operations with Resource Manager (Operacje](../management/view-activity-logs.md)inspekcji za pomocą Resource Manager ).
* Aby dowiedzieć się więcej o akcjach w celu określenia błędów podczas wdrażania, [zobacz Wyświetlanie operacji wdrażania](deployment-history.md).
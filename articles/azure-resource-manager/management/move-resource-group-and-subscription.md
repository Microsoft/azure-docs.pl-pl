---
title: Przenoszenie zasobów do nowej subskrypcji lub grupy zasobów
description: Użyj Azure Resource Manager, aby przenieść zasoby do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 04/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7a50ecc6081f8fa7c7600ddf1f98a95eceafa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784627"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji

W tym artykule pokazano, jak przenieść zasoby platformy Azure do innej subskrypcji platformy Azure lub innej grupy zasobów w ramach tej samej subskrypcji. Do przenoszenia zasobów możesz użyć witryny Azure Portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

Zarówno grupa źródłowa, jak i docelowa są zablokowane podczas operacji przenoszenia. Operacje zapisu i usuwania na grupach zasobów są blokowane do momentu zakończenia przenoszenia. Ta blokada oznacza, że nie można dodawać, aktualizować ani usuwać zasobów w grupach zasobów. Nie oznacza to, że zasoby są zablokowane. Jeśli na przykład przeniesiesz serwer logiczny Azure SQL jego bazy danych do nowej grupy zasobów lub subskrypcji, aplikacje, które korzystają z baz danych, nie będą mieć przestoju. Nadal mogą odczytywać i zapisywać w bazach danych. Blokada może trwać maksymalnie cztery godziny, ale większość przenosi się w znacznie krótszym czasie.

Przeniesienie zasobu powoduje jedynie przeniesienie go do nowej grupy zasobów lub subskrypcji. Operacja nie może zmienić lokalizacji zasobu.

## <a name="changed-resource-id"></a>Zmieniono identyfikator zasobu

Podczas przenoszenia zasobu zmieniasz jego identyfikator zasobu. Standardowy format identyfikatora zasobu to `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}` . Podczas przenoszenia zasobu do nowej grupy zasobów lub subskrypcji należy zmienić co najmniej jedną wartość w tej ścieżce.

Jeśli używasz identyfikatora zasobu w dowolnym miejscu, musisz zmienić wartość. Jeśli na przykład w portalu masz [niestandardowy pulpit](../../azure-portal/quickstart-portal-dashboard-azure-cli.md) nawigacyjny, który odwołuje się do identyfikatora zasobu, musisz zaktualizować tę wartość. Poszukaj skryptów lub szablonów, które należy zaktualizować dla nowego identyfikatora zasobu.

## <a name="checklist-before-moving-resources"></a>Sporządzenie listy kontrolnej przed przeniesieniem zasobów

Przed przeniesieniem zasobu należy wykonać kilka ważnych czynności. Dzięki sprawdzeniu tych warunków można uniknąć błędów.

1. Zasoby, które chcesz przenieść, muszą obsługiwać operację przenoszenia. Aby uzyskać listę zasobów, które obsługują przenoszenie, zobacz [Obsługa operacji przenoszenia dla zasobów](move-support-resources.md).

1. Niektóre usługi mają określone ograniczenia lub wymagania dotyczące przenoszenia zasobów. Jeśli przenosisz dowolną z następujących usług, przed przeniesieniem sprawdź te wskazówki.

   * Jeśli używasz usługi Azure Stack Hub, nie możesz przenosić zasobów między grupami.
   * [App Services dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services dotyczące przenoszenia](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Wskazówki dotyczące przenoszenia klasycznego modelu wdrażania](./move-limitations/classic-model-move-limitations.md) — klasyczne obliczenia, klasyczny magazyn, klasyczne sieci wirtualne i Cloud Services
   * [Wskazówki dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md)
   * [Wskazówki dotyczące przenoszenia usług Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Virtual Machines dotyczące przenoszenia](./move-limitations/virtual-machines-move-limitations.md)
   * Aby przenieść subskrypcję platformy Azure do nowej grupy zarządzania, [zobacz Przenoszenie subskrypcji](../../governance/management-groups/manage.md#move-subscriptions).

1. Jeśli przeniesiesz zasób, który ma rolę platformy Azure przypisaną bezpośrednio do zasobu (lub zasobu podrzędnego), przypisanie roli nie zostanie przeniesione i zostanie oddzielone. Po zakończeniu przenoszenia należy ponownie utworzyć przypisanie roli. Po pewnym czasie oddzielone przypisanie roli zostanie automatycznie usunięte, ale zalecamy usunięcie przypisania roli przed przeniesieniem.

    Aby uzyskać informacje na temat zarządzania przypisaniami ról, zobacz List Azure role assignments (Wyświetlanie [listy przypisań ról platformy Azure) i](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) [Assign Azure roles (Przypisywanie ról platformy Azure).](../../role-based-access-control/role-assignments-portal.md)

1. Subskrypcje źródłowe i docelowe muszą być aktywne. Jeśli masz problemy z włączeniem wyłączonego konta, utwórz [wniosek o pomoc techniczną platformy Azure.](../../azure-portal/supportability/how-to-create-azure-support-request.md) Wybierz **pozycję Zarządzanie subskrypcjami** jako typ problemu.

1. Subskrypcje źródłowe i docelowe muszą istnieć w tej samej [Azure Active Directory dzierżawie](../../active-directory/develop/quickstart-create-new-tenant.md). Aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy, użyj Azure PowerShell wiersza polecenia platformy Azure.

   Na Azure PowerShell użyj:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Jeśli identyfikatory dzierżawy dla subskrypcji źródłowej i docelowej nie są takie same, użyj następujących metod, aby uzgodnić identyfikatory dzierżaw:

   * [Transfer ownership of an Azure subscription to another account](../../cost-management-billing/manage/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto)
   * [Jak skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Subskrypcja docelowa musi być zarejestrowana dla dostawcy przenoszonego zasobu. Jeśli nie, zostanie wyświetlony komunikat o błędzie informujący, że subskrypcja nie jest **zarejestrowana dla typu zasobu**. Ten błąd może wystąpić podczas przenoszenia zasobu do nowej subskrypcji, ale ta subskrypcja nigdy nie była używana z tym typem zasobu.

   W przypadku programu PowerShell użyj następujących poleceń, aby uzyskać stan rejestracji:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Aby zarejestrować dostawcę zasobów, użyj:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   W przypadku interfejsu wiersza polecenia platformy Azure użyj następujących poleceń, aby uzyskać stan rejestracji:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Aby zarejestrować dostawcę zasobów, użyj:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Konto przenoszące zasoby musi mieć co najmniej następujące uprawnienia:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** w źródłowej grupie zasobów.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** w docelowej grupie zasobów.

1. Przed przeniesieniem zasobów sprawdź limity przydziału subskrypcji, do których są przenoszące zasoby. Jeśli przeniesienie zasobów oznacza, że subskrypcja przekroczy limity, należy sprawdzić, czy można zażądać zwiększenia limitu przydziału. Aby uzyskać listę limitów i sposób żądania zwiększenia, zobacz [Azure subscription and service limits, quotas, and constraints](../../azure-resource-manager/management/azure-subscription-service-limits.md)(Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure).

1. **W przypadku przenoszenia między subskrypcjami zasób i jego zasoby zależne muszą znajdować się w tej samej grupie zasobów i muszą być przenoszone razem.** Na przykład maszyna wirtualna z dyskami zarządzanymi wymagałaby, aby maszyna wirtualna i dyski zarządzane zostały przeniesione razem wraz z innymi zasobami zależnym.

   Jeśli przenosisz zasób do nowej subskrypcji, sprawdź, czy zasób ma jakiekolwiek zasoby zależne i czy znajduje się w tej samej grupie zasobów. Jeśli zasoby nie są w tej samej grupie zasobów, sprawdź, czy zasoby można połączyć w tę samą grupę zasobów. Jeśli tak, przenieś wszystkie te zasoby do tej samej grupy zasobów przy użyciu operacji przenoszenia między grupami zasobów.

   Aby uzyskać więcej informacji, zobacz [Scenariusz przenoszenia między subskrypcjami.](#scenario-for-move-across-subscriptions)

## <a name="scenario-for-move-across-subscriptions"></a>Scenariusz przenoszenia między subskrypcjami

Przenoszenie zasobów z jednej subskrypcji do innej jest procesem trzyetapowym:

![scenariusz przenoszenia między subskrypcjami](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Dla celów ilustratora mamy tylko jeden zasób zależny.

* Krok 1. Jeśli zasoby zależne są rozproszone w różnych grupach zasobów, najpierw przenieś je do jednej grupy zasobów.
* Krok 2. Przeniesienie zasobu i zasobów zależnych razem z subskrypcji źródłowej do subskrypcji docelowej.
* Krok 3. Opcjonalnie możesz ponownie rozesłać zasoby zależne do różnych grup zasobów w ramach subskrypcji docelowej.

## <a name="validate-move"></a>Weryfikowanie przeniesienia

Operacja [weryfikacji przenoszenia](/rest/api/resources/resources/moveresources) umożliwia przetestowanie scenariusza przenoszenia bez przenoszenia zasobów. Użyj tej operacji, aby sprawdzić, czy przenoszenie zakończy się pomyślnie. Walidacja jest wywoływana automatycznie podczas wysyłania żądania przeniesienia. Tej operacji należy używać tylko wtedy, gdy trzeba wstępnie kreślić wyniki. Do uruchomienia tej operacji potrzebne są:

* nazwa źródłowej grupy zasobów
* identyfikator zasobu docelowej grupy zasobów
* identyfikator zasobu dla każdego zasobu do przeniesienia
* token [dostępu](/rest/api/azure/#acquire-an-access-token) dla twojego konta

Wyślij następujące żądanie:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Z treścią żądania:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Jeśli żądanie jest poprawnie sformatowane, operacja zwraca:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Kod stanu 202 wskazuje, że żądanie weryfikacji zostało zaakceptowane, ale nie zostało jeszcze określone, czy operacja przenoszenia zakończy się pomyślnie. Wartość zawiera adres URL, który umożliwia sprawdzenie `location` stanu długotrwałej operacji.

Aby sprawdzić stan, wyślij następujące żądanie:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Gdy operacja jest nadal uruchomiona, nadal otrzymujesz kod stanu 202. Przed podjęciem próby odczekaj liczbę sekund `retry-after` wskazaną w wartości . Jeśli operacja przenoszenia zostanie pomyślnie zweryfikowana, otrzymasz kod stanu 204. Jeśli weryfikacja przenoszenia zakończy się niepowodzeniem, zostanie wyświetlony komunikat o błędzie, taki jak:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Używanie portalu

Aby przenieść zasoby, wybierz grupę zasobów zawierającą te zasoby.

Podczas wyświetlania grupy zasobów opcja przenoszenia jest wyłączona.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="opcja przenoszenia jest wyłączona":::

Aby włączyć opcję przenoszenia, wybierz zasoby, które chcesz przenieść. Aby zaznaczyć wszystkie zasoby, zaznacz pole wyboru w górnej części listy. Możesz też wybrać zasoby pojedynczo. Po wybraniu zasobów opcja przenoszenia jest włączona.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="wybieranie zasobów":::

Wybierz przycisk **Przenieś.**

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="opcje przenoszenia":::

Ten przycisk udostępnia trzy opcje:

* Przejdź do nowej grupy zasobów.
* Przejdź do nowej subskrypcji.
* Przejście do nowego regionu. Aby zmienić regiony, zobacz [Przenoszenie zasobów między regionami (z grupy zasobów).](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json)

Wybierz, czy przenosisz zasoby do nowej grupy zasobów, czy nowej subskrypcji.

Wybierz docelową grupę zasobów. Potwierdzić, że musisz zaktualizować skrypty dla tych zasobów, a następnie wybierz przycisk **OK.** Jeśli wybrano przejście do nowej subskrypcji, musisz również wybrać subskrypcję docelową.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="wybieranie miejsca docelowego":::

Po zweryfikowaniu, czy zasoby można przenieść, zostanie wyświetlony komunikat z powiadomieniem, że operacja przenoszenia jest uruchomiona.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="Powiadomienie":::

Po zakończeniu tego procesu zostaniesz powiadomiony o wyniku.

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj [polecenia Move-AzResource.](/powershell/module/az.resources/move-azresource) W poniższym przykładzie pokazano, jak przenieść kilka zasobów do nowej grupy zasobów.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Aby przejść do nowej subskrypcji, dołącz wartość `DestinationSubscriptionId` parametru .

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj [polecenia az resource move.](/cli/azure/resource#az_resource_move) Podaj identyfikatory zasobów do przeniesienia. W poniższym przykładzie pokazano, jak przenieść kilka zasobów do nowej grupy zasobów. W `--ids` parametrze podaj rozdzielaną spacjami listę identyfikatorów zasobów do przeniesienia.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Aby przejść do nowej subskrypcji, podaj `--destination-subscription-id` parametr .

## <a name="use-rest-api"></a>Korzystanie z interfejsu API REST

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj [operacji Przenoszenia](/rest/api/resources/resources/moveresources) zasobów.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

W treści żądania należy określić docelową grupę zasobów i zasoby do przeniesienia.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie: Moja operacja przenoszenia zasobu, która zwykle trwa kilka minut, jest uruchamiana przez prawie godzinę. Czy coś jest nie tak?**

Przenoszenie zasobu jest złożoną operacją, która ma różne fazy. Może to obejmować nie tylko dostawcę zasobów zasobu, który próbujesz przenieść. Ze względu na zależności między dostawcami zasobów Azure Resource Manager 4 godziny na ukończenie operacji. Ten okres daje dostawcom zasobów możliwość odzyskania po przejściowych problemach. Jeśli żądanie przeniesienia znajduje się w ciągu czterech godzin, operacja będzie się powtarzać i może się powieść. Źródłowe i docelowe grupy zasobów są w tym czasie zablokowane, aby uniknąć problemów ze spójnością.

**Pytanie: Dlaczego moja grupa zasobów jest zablokowana na cztery godziny podczas przenoszenia zasobów?**

Żądanie przeniesienia może zakończyć się maksymalnie przez cztery godziny. Aby zapobiec modyfikowaniu przenoszonego zasobu, podczas przenoszenia zasobów zarówno źródłową, jak i docelową grupę zasobów są blokowane.

Żądanie przeniesienia ma dwie fazy. W pierwszej fazie zasób jest przenoszony. W drugiej fazie powiadomienia są wysyłane do innych dostawców zasobów, którzy są zależni od przenoszonego zasobu. Grupę zasobów można zablokować na całe cztery godziny, gdy dostawca zasobów zakończy się niepowodzeniem w jednej z faz. W dozwolonym czasie Resource Manager ponowne próby kroku, który zakończył się niepowodzeniem.

Jeśli zasobu nie można przenieść w ciągu czterech godzin, Resource Manager obie grupy zasobów. Zasoby, które zostały pomyślnie przeniesione, znajdują się w docelowej grupie zasobów. Zasoby, których nie można przenieść, zostaną pozostawione w źródłowej grupie zasobów.

**Pytanie: Jakie są implikacje zablokowania źródłowej i docelowej grupy zasobów podczas przenoszenia zasobu?**

Blokada uniemożliwia usunięcie dowolnej grupy zasobów, utworzenie nowego zasobu w grupie zasobów lub usunięcie wszystkich zasobów uczestniczących w przenoszeniu.

Na poniższej ilustracji przedstawiono komunikat o błędzie z Azure Portal gdy użytkownik próbuje usunąć grupę zasobów, która jest częścią trwającego przenoszenia.

![Komunikat o błędzie przenoszenia podczas próby usunięcia](./media/move-resource-group-and-subscription/move-error-delete.png)

**Pytanie: Co oznacza kod błędu "MissingMoveDependentResources"?**

Podczas przenoszenia zasobu zasoby zależne muszą istnieć w docelowej grupie zasobów lub subskrypcji albo być uwzględnione w żądaniu przeniesienia. Kod błędu MissingMoveDependentResources jest wyświetlany, gdy zasób zależny nie spełnia tego wymagania. Komunikat o błędzie zawiera szczegółowe informacje o zasobie zależnym, który musi zostać uwzględniony w żądaniu przeniesienia.

Na przykład przeniesienie maszyny wirtualnej może wymagać przenoszenia siedmiu typów zasobów z trzema różnymi dostawcami zasobów. Tymi dostawcami zasobów i typami zasobów są:

* Microsoft.Compute

  * virtualMachines (maszyny wirtualne)
  * Dysków
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * konta magazynu

Innym powszechnym przykładem jest przeniesienie sieci wirtualnej. Może być trzeba przenieść kilka innych zasobów skojarzonych z tą siecią wirtualną. Żądanie przeniesienia może wymagać przeniesienia publicznych adresów IP, tabel tras, bram sieci wirtualnej, sieciowych grup zabezpieczeń i innych.

**Pytanie: Dlaczego nie mogę przenieść niektórych zasobów na platformie Azure?**

Obecnie nie wszystkie zasoby na platformie Azure obsługują przenoszenie. Aby uzyskać listę zasobów, które obsługują przenoszenie, zobacz [Obsługa operacji przenoszenia dla zasobów](move-support-resources.md).

**Pytanie: Ile zasobów można przenieść w ramach jednej operacji?**

Jeśli to możliwe, należy dzielić duże operacje przenoszenia na oddzielne operacje przenoszenia. Resource Manager natychmiast zwraca błąd, gdy w jednej operacji znajduje się więcej niż 800 zasobów. Jednak przekierowanie mniej niż 800 zasobów może również się nie powieść, ponieważ przekieruje limit czasu.

**Pytanie: Jakie jest znaczenie błędu, że zasób nie jest w stanie sukcesu?**

Gdy zostanie wyświetlony komunikat o błędzie informujący, że nie można przenieść zasobu, ponieważ nie jest on w stanie sukcesu, może to być zasób zależny, który blokuje przeniesienie. Zazwyczaj kod błędu to **MoveCannotProceedWithResourcesNotInSucceededState.**

Jeśli źródło lub docelowa grupa zasobów zawiera sieć wirtualną, podczas przenoszenia sprawdzane są stany wszystkich zasobów zależnych dla sieci wirtualnej. Sprawdzanie obejmuje te zasoby bezpośrednio i pośrednio zależne od sieci wirtualnej. Jeśli którykolwiek z tych zasobów jest w stanie awarii, przeniesienie zostanie zablokowane. Na przykład jeśli maszyna wirtualna, która korzysta z sieci wirtualnej, nie powiodła się, przeniesienie zostanie zablokowane. Przenoszenie jest blokowane nawet wtedy, gdy maszyna wirtualna nie jest jednym z przenoszonego zasobu i nie znajduje się w jednej z grup zasobów dla przeniesienia.

Po otrzymaniu tego błędu masz dwie opcje. Przenieś zasoby do grupy zasobów, która nie ma sieci wirtualnej, lub skontaktuj się z [pomocą techniczną.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę zasobów, które obsługują przenoszenie, zobacz [Obsługa operacji przenoszenia dla zasobów](move-support-resources.md).

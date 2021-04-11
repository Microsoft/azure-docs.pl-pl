---
title: Przenoszenie zasobów do nowej subskrypcji lub grupy zasobów
description: Użyj Azure Resource Manager, aby przenieść zasoby do nowej grupy zasobów lub subskrypcji.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 800e605571ae18b008a86b4add4b0b2adce9c140
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078387"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji

W tym artykule opisano sposób przenoszenia zasobów platformy Azure do innej subskrypcji platformy Azure lub innej grupy zasobów w ramach tej samej subskrypcji. Do przenoszenia zasobów możesz użyć witryny Azure Portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

Zarówno Grupa źródłowa, jak i Grupa docelowa są zablokowane podczas operacji przenoszenia. Operacje zapisu i usuwania na grupach zasobów są blokowane do momentu zakończenia przenoszenia. Ta blokada oznacza, że nie można dodawać, aktualizować ani usuwać zasobów w grupach zasobów. Nie oznacza to, że zasoby są zamrożone. Jeśli na przykład przeniesiesz serwer logiczny usługi Azure SQL i jego bazy danych do nowej grupy zasobów lub subskrypcji, aplikacje korzystające z baz danych nie będą miały żadnych przestojów. Nadal mogą odczytywać i zapisywać bazy danych. Blokada może trwać maksymalnie cztery godziny, ale większość ruchów kończy się w znacznie krótszym czasie.

Przeniesienie zasobu powoduje jedynie przeniesienie go do nowej grupy zasobów lub subskrypcji. Operacja nie może zmienić lokalizacji zasobu.

## <a name="changed-resource-id"></a>Zmieniony identyfikator zasobu

Podczas przenoszenia zasobu należy zmienić jego identyfikator zasobu. Standardowy format identyfikatora zasobu to `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}` . Po przeniesieniu zasobu do nowej grupy zasobów lub subskrypcji należy zmienić jedną lub więcej wartości w tej ścieżce.

Jeśli używasz identyfikatora zasobu w dowolnym miejscu, musisz zmienić tę wartość. Na przykład jeśli w portalu znajduje się [niestandardowy pulpit nawigacyjny](../../azure-portal/quickstart-portal-dashboard-azure-cli.md) , który odwołuje się do identyfikatora zasobu, należy zaktualizować tę wartość. Poszukaj wszelkich skryptów lub szablonów, które należy zaktualizować dla nowego identyfikatora zasobu.

## <a name="checklist-before-moving-resources"></a>Sporządzenie listy kontrolnej przed przeniesieniem zasobów

Przed przeniesieniem zasobu należy wykonać kilka ważnych czynności. Dzięki sprawdzeniu tych warunków można uniknąć błędów.

1. Zasoby, które mają zostać przeniesione, muszą obsługiwać operację przenoszenia. Aby zapoznać się z listą obsługiwanych zasobów, zobacz [przenoszenie obsługi zasobów](move-support-resources.md).

1. Niektóre usługi mają określone ograniczenia lub wymagania dotyczące przemieszczania zasobów. Jeśli przenosisz dowolne z następujących usług, przed przeniesieniem Sprawdź, czy zostały podane wskazówki.

   * Jeśli używasz centrum Azure Stack, nie możesz przenosić zasobów między grupami.
   * [App Services wskazówki dotyczące przenoszenia](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services wskazówki dotyczące przenoszenia](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klasyczny model wdrażania — wskazówki dotyczące przenoszenia](./move-limitations/classic-model-move-limitations.md) klasycznego, klasycznego magazynu, klasycznych sieci wirtualnych i Cloud Services
   * [Wskazówki dotyczące przenoszenia sieci](./move-limitations/networking-move-limitations.md)
   * [Recovery Services wskazówki dotyczące przenoszenia](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Virtual Machines wskazówki dotyczące przenoszenia](./move-limitations/virtual-machines-move-limitations.md)
   * Aby przenieść subskrypcję platformy Azure do nowej grupy zarządzania, zobacz [przenoszenie subskrypcji](../../governance/management-groups/manage.md#move-subscriptions).

1. W przypadku przeniesienia zasobu z rolą platformy Azure przypisaną bezpośrednio do zasobu (lub zasobu podrzędnego) przypisanie roli nie zostanie przeniesione i zostanie oddzielone. Po przeniesieniu należy ponownie utworzyć przypisanie roli. Ostatecznie przypisanie oddzielonej roli zostanie automatycznie usunięte, ale zalecamy usunięcie przypisania roli przed przeniesieniem.

    Informacje o sposobach zarządzania przypisaniami ról można znaleźć w temacie [Lista przypisań ról platformy Azure](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) i [Przypisywanie ról platformy Azure](../../role-based-access-control/role-assignments-portal.md).

1. Subskrypcje źródłowe i docelowe muszą być aktywne. Jeśli masz problemy z włączeniem wyłączonego konta, [Utwórz żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Wybierz pozycję **Zarządzanie subskrypcją** dla typu problemu.

1. Subskrypcje źródłowe i docelowe muszą znajdować się w tej samej [dzierżawie Azure Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md). Aby sprawdzić, czy obie subskrypcje mają ten sam identyfikator dzierżawy, użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

   Aby uzyskać Azure PowerShell, użyj:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Jeśli identyfikatory dzierżawy dla subskrypcji źródłowej i docelowej nie są takie same, użyj następujących metod, aby uzgodnić identyfikatory dzierżawców:

   * [Transfer ownership of an Azure subscription to another account](../../cost-management-billing/manage/billing-subscription-transfer.md) (Przenoszenie własności subskrypcji platformy Azure na inne konto)
   * [Jak skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Subskrypcja docelowa musi być zarejestrowana dla dostawcy przenoszonego zasobu. W przeciwnym razie zostanie wyświetlony komunikat o błędzie informujący, że **subskrypcja nie jest zarejestrowana dla typu zasobu**. Ten błąd może pojawić się podczas przeniesienia zasobu do nowej subskrypcji, ale ta subskrypcja nigdy nie była używana z typem zasobu.

   W przypadku programu PowerShell Użyj następujących poleceń, aby uzyskać stan rejestracji:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Aby zarejestrować dostawcę zasobów, użyj:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   W przypadku interfejsu wiersza polecenia platformy Azure Użyj następujących poleceń, aby uzyskać stan rejestracji:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Aby zarejestrować dostawcę zasobów, użyj:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Konto przenoszące zasoby musi mieć co najmniej następujące uprawnienia:

   * **Microsoft. resources/subscriptions/resourceGroups/moveResources/Action** w źródłowej grupie zasobów.
   * **Microsoft. resources/subscriptions/resourceGroups/Write** w docelowej grupie zasobów.

1. Przed przeniesieniem zasobów Sprawdź przydziały subskrypcji dla subskrypcji, do której chcesz przenieść zasoby. Jeśli przeniesienie zasobów oznacza, że subskrypcja przekroczy jej limity, należy sprawdzić, czy można zażądać zwiększenia limitu przydziału. Aby zapoznać się z listą limitów i jak zażądać zwiększenia, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

1. **W przypadku przechodzenia między subskrypcjami zasób i jego zasoby zależne muszą znajdować się w tej samej grupie zasobów i muszą zostać przeniesione ze sobą.** Na przykład maszyna wirtualna z dyskami zarządzanymi będzie wymagała przeniesienia maszyny wirtualnej i zarządzanych dysków wraz z innymi zasobami zależnymi.

   Jeśli przenosisz zasób do nowej subskrypcji, sprawdź, czy zasób zawiera zasoby zależne i czy znajdują się one w tej samej grupie zasobów. Jeśli zasoby nie znajdują się w tej samej grupie zasobów, sprawdź, czy zasoby mogą być połączone z tą samą grupą zasobów. W takim przypadku należy przenieść wszystkie te zasoby do tej samej grupy zasobów przy użyciu operacji przenoszenia między grupami zasobów.

   Aby uzyskać więcej informacji, zobacz [scenariusz przenoszenia między subskrypcjami](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scenariusz przenoszenia między subskrypcjami

Przeniesienie zasobów z jednej subskrypcji do innej to proces trójstanowy:

![scenariusz przenoszenia między subskrypcjami](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Na potrzeby ilustracji mamy tylko jeden zasób zależny.

* Krok 1. Jeśli zasoby zależne są dystrybuowane w różnych grupach zasobów, najpierw Przenieś je do jednej grupy zasobów.
* Krok 2. przeniesienie zasobu i zasobów zależnych ze źródłowej subskrypcji do subskrypcji docelowej.
* Krok 3. Opcjonalnie można ponownie rozesłać zasoby zależne do różnych grup zasobów w ramach subskrypcji docelowej.

## <a name="validate-move"></a>Weryfikuj przeniesienie

[Operacja Weryfikuj przeniesienie](/rest/api/resources/resources/moveresources) umożliwia testowanie scenariusza przenoszenia bez rzeczywistego przenoszenia zasobów. Użyj tej operacji, aby sprawdzić, czy przeniesienie zakończy się pomyślnie. Walidacja jest wywoływana automatycznie podczas wysyłania żądania przeniesienia. Tej operacji należy używać tylko wtedy, gdy trzeba określić wyniki. Do uruchomienia tej operacji potrzebne są:

* Nazwa źródłowej grupy zasobów
* Identyfikator zasobu docelowej grupy zasobów
* Identyfikator zasobu dla każdego zasobu do przeniesienia
* [token dostępu](/rest/api/azure/#acquire-an-access-token) dla Twojego konta

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

Jeśli żądanie jest sformatowane prawidłowo, operacja zwraca:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Kod stanu 202 wskazuje, że żądanie weryfikacji zostało zaakceptowane, ale jeszcze nie zostało określone, jeśli operacja przenoszenia zakończy się pomyślnie. `location`Wartość zawiera adres URL, którego można użyć do sprawdzenia stanu długotrwałej operacji.  

Aby sprawdzić stan, wyślij następujące żądanie:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Gdy operacja jest nadal uruchomiona, nadal otrzymujesz kod stanu 202. Przed ponowieniem próby poczekaj liczbę sekund określoną w `retry-after` wartości. Jeśli operacja przenoszenia zostanie pomyślnie zweryfikowana, zostanie wyświetlony kod stanu 204. Jeśli weryfikacja przenoszenia nie powiedzie się, zostanie wyświetlony komunikat o błędzie, taki jak:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Używanie portalu

Aby przenieść zasoby, wybierz grupę zasobów zawierającą te zasoby.

Podczas wyświetlania grupy zasobów opcja Move jest wyłączona.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="Opcja przenoszenia wyłączona":::

Aby włączyć opcję Przenieś, wybierz zasoby, które chcesz przenieść. Aby zaznaczyć wszystkie zasoby, zaznacz pole wyboru znajdujące się u góry listy. Lub zaznacz opcję zasoby pojedynczo. Po wybraniu zasobów opcja Move jest włączona.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="Wybieranie zasobów":::

Wybierz przycisk **Przenieś** .

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="Opcje przenoszenia":::

Ten przycisk udostępnia trzy opcje:

* Przenieś do nowej grupy zasobów.
* Przejdź do nowej subskrypcji.
* Przejdź do nowego regionu. Aby zmienić regiony, zobacz [przenoszenie zasobów między regionami (z grupy zasobów)](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json).

Wybierz, czy przenosisz zasoby do nowej grupy zasobów, czy do nowej subskrypcji.

Wybierz docelową grupę zasobów. Potwierdź, że musisz zaktualizować skrypty dla tych zasobów, a następnie wybierz **przycisk OK**. W przypadku wybrania opcji przejścia do nowej subskrypcji należy również wybrać subskrypcję docelową.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="Wybierz lokalizację docelową":::

Po zweryfikowaniu, że zasoby można przenieść, zobaczysz powiadomienie, że operacja przenoszenia jest uruchomiona.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="zawiadomienie":::

Po zakończeniu otrzymasz powiadomienie o wyniku.

Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów z przeniesieniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj polecenia [Move-AzResource](/powershell/module/az.resources/move-azresource) . Poniższy przykład pokazuje, jak przenieść kilka zasobów do nowej grupy zasobów.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Aby przejść do nowej subskrypcji, Dołącz wartość `DestinationSubscriptionId` parametru.

Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów z przeniesieniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj polecenia [AZ Resource Move](/cli/azure/resource#az-resource-move) . Podaj identyfikatory zasobów, które mają zostać przeniesione. Poniższy przykład pokazuje, jak przenieść kilka zasobów do nowej grupy zasobów. W `--ids` parametrze Podaj rozdzieloną spacjami listę identyfikatorów zasobów do przeniesienia.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Aby przejść do nowej subskrypcji, podaj `--destination-subscription-id` parametr.

Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów z przeniesieniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="use-rest-api"></a>Korzystanie z interfejsu API REST

Aby przenieść istniejące zasoby do innej grupy zasobów lub subskrypcji, użyj operacji [Przenieś zasoby](/rest/api/resources/resources/moveresources) .

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

W treści żądania należy określić docelową grupę zasobów i zasoby, które mają zostać przeniesione.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów z przeniesieniem zasobów platformy Azure do nowej grupy zasobów lub subskrypcji](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie: Operacja przenoszenia zasobu, która zazwyczaj trwa kilka minut, była uruchomiona przez prawie godzinę. Czy wystąpił problem?**

Przeniesienie zasobu jest skomplikowaną operacją, która ma różne etapy. Może dotyczyć tylko dostawcy zasobów zasobu, który próbujesz przenieść. Ze względu na zależności między dostawcami zasobów, Azure Resource Manager umożliwia ukończenie operacji przez 4 godziny. Ten okres zapewnia dostawcom zasobów możliwość odzyskania sprawności od problemów przejściowych. Jeśli Twoje żądanie przeniesienia jest w okresie czterech godzin, operacja kontynuuje próbę wykonania i może nadal się powieść. Źródłowe i docelowe grupy zasobów są zablokowane w tym czasie, aby uniknąć problemów ze spójnością.

**Pytanie: Dlaczego moja grupa zasobów została zablokowana przez cztery godziny podczas przenoszenia zasobów?**

Żądanie przeniesienia jest dozwolone maksymalnie cztery godziny. Aby zapobiec modyfikacji zasobów, zarówno źródłowa, jak i docelowa Grupa zasobów są blokowane podczas przenoszenia zasobów.

W żądaniu przeniesienia istnieją dwie fazy. W pierwszej fazie zasób jest przenoszony. W drugiej fazie powiadomienia są wysyłane do innych dostawców zasobów, które są zależne od przenoszonego zasobu. Grupę zasobów można zablokować przez całe cztery godziny, gdy dostawca zasobów zakończy się niepowodzeniem. W dozwolonym czasie Menedżer zasobów ponawianie próby zakończonego niepowodzeniem.

Jeśli zasób nie może zostać przeniesiony w ciągu czterech godzin, Menedżer zasobów odblokowuje obie grupy zasobów. Zasoby, które zostały przeniesione pomyślnie, znajdują się w docelowej grupie zasobów. Zasoby, które nie zostały przeniesione, są pozostawione źródłową grupą zasobów.

**Pytanie: Jakie są konsekwencje źródłowej i docelowej grupy zasobów, które są blokowane podczas przenoszenia zasobów?**

Blokada uniemożliwia usunięcie grupy zasobów, utworzenie nowego zasobu w grupie zasobów albo usunięcie dowolnego z zasobów związanych z przenoszeniem.

Na poniższej ilustracji przedstawiono komunikat o błędzie z Azure Portal, gdy użytkownik próbuje usunąć grupę zasobów, która jest częścią trwającego przenoszenia.

![Przenieś komunikat o błędzie podczas próby usunięcia](./media/move-resource-group-and-subscription/move-error-delete.png)

**Pytanie: co oznacza kod błędu "MissingMoveDependentResources"?**

Podczas przenoszenia zasobu jego zależne zasoby muszą znajdować się w docelowej grupie zasobów lub subskrypcji lub być dołączone do żądania przeniesienia. Kod błędu MissingMoveDependentResources można uzyskać, gdy zasób zależny nie spełnia tego wymagania. Komunikat o błędzie zawiera szczegółowe informacje o zasobach zależnych, które należy uwzględnić w żądaniu przeniesienia.

Na przykład przeniesienie maszyny wirtualnej może wymagać przeniesienia siedmiu typów zasobów z trzema różnymi dostawcami zasobów. Te dostawcy zasobów i typy są:

* Microsoft.Compute

  * virtualMachines
  * dysku
* Microsoft.Network
  * networkInterfaces
  * Adresów publicipaddress
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Inny typowy przykład obejmuje przeniesienie sieci wirtualnej. Może być konieczne przeniesienie kilku innych zasobów skojarzonych z tą siecią wirtualną. Żądanie Move może wymagać przeniesienia publicznych adresów IP, tabel tras, bram sieci wirtualnej, sieciowych grup zabezpieczeń i innych.

**Pytanie: Dlaczego nie mogę przenieść niektórych zasobów na platformę Azure?**

Obecnie nie wszystkie zasoby w ramach pomocy technicznej platformy Azure są przenoszone. Aby zapoznać się z listą zasobów, które obsługują przenoszenie, zobacz [przenoszenie obsługi zasobów](move-support-resources.md).

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z listą obsługiwanych zasobów, zobacz [przenoszenie obsługi zasobów](move-support-resources.md).

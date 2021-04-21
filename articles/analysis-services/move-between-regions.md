---
title: Przenoszenie Azure Analysis Services do innego regionu | Microsoft Docs
description: Opisuje sposób przenoszenia zasobu Azure Analysis Services do innego regionu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 2b698ffaddb4bc818eaabda34022ab58ff05fe5f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786355"
---
# <a name="move-analysis-services-to-a-different-region"></a>Przenoszenie Analysis Services do innego regionu

W tym artykule opisano sposób przenoszenia zasobu Analysis Services do innego regionu świadczenia usługi Azure. Możesz przenieść serwer do innego regionu z kilku powodów, na przykład, aby korzystać z regionu świadczenia usługi Azure bliżej użytkowników, korzystać z planów usług obsługiwanych tylko w określonych regionach lub spełniać wewnętrzne wymagania dotyczące zasad i ładu. 

W tym i skojarzonych połączonych artykułach dowiesz się, jak:

> [!div class="checklist"]
> 
> * Tworzenie kopii zapasowej bazy danych modelu serwera źródłowego w [magazynie obiektów blob.](../storage/blobs/storage-blobs-introduction.md)
> * Wyeksportuj [szablon zasobu serwera źródłowego](../azure-resource-manager/templates/overview.md).
> * Pobierz sygnaturę [dostępu współdzielonego (SAS) magazynu.](../storage/common/storage-sas-overview.md)
> * Zmodyfikuj szablon zasobu.
> * Wd wdrożenie szablonu w celu utworzenia nowego serwera docelowego.
> * Przywracanie modelowej bazy danych na nowy serwer docelowy.
> * Sprawdź nowy serwer docelowy i bazę danych.
> * Usuń serwer źródłowy.

W tym artykule opisano używanie szablonu zasobu do migrowania pojedynczego Analysis Services z podstawową konfiguracją do innego *regionu* i grupy zasobów w tej samej subskrypcji.  Użycie szablonu zachowuje skonfigurowane właściwości serwera, dzięki czemu serwer docelowy jest skonfigurowany z tą samą właściwością, z wyjątkiem regionu i grupy zasobów, co serwer źródłowy. W tym artykule nie opisano przenoszenia skojarzonych zasobów, które mogą być częścią tej samej grupy zasobów, takiej jak źródło danych, magazyn i zasoby bramy. 

Przed przeniesieniem serwera do innego regionu zaleca się utworzenie szczegółowego planu. Rozważ dodatkowe zasoby, takie jak bramy i magazyn, które również mogą wymagać przenoszony. W przypadku dowolnego planu ważne jest ukończenie co najmniej jednej operacji przenoszenia wersji próbnej przy użyciu serwerów testowych przed przeniesieniem serwera produkcyjnego.

> [!IMPORTANT]
> Aplikacje klienckie i parametry połączenia łączą się z Analysis Services przy użyciu pełnej nazwy serwera, która jest URI, który zawiera region, w którym znajduje się serwer. Na przykład `asazure://westcentralus.asazure.windows.net/advworks01`. Podczas przenoszenia serwera do innego regionu skutecznie tworzysz nowy zasób serwera w innym regionie, który będzie miał inny region w nazwie serwera URI. Aplikacje klienckie i parametry połączenia używane w skryptach muszą łączyć się z nowym serwerem przy użyciu nowego nazwy URI serwera. Użycie [aliasu nazwy serwera](analysis-services-server-alias.md) może zmniejszyć liczbę miejsc, w których należy zmienić adres URI nazwy serwera, ale należy zaimplementować przed przeniesieniem regionu.

> [!IMPORTANT]
> Regiony platformy Azure używają różnych zakresów adresów IP. Jeśli masz wyjątki zapory skonfigurowane dla regionu, w którym znajduje się serwer i/lub konto magazynu, może być konieczne skonfigurowanie innego zakresu adresów IP. Aby dowiedzieć się więcej, zobacz [często zadawane pytania dotyczące łączności Analysis Services sieci.](analysis-services-network-faq.md)

> [!NOTE]
> W tym artykule opisano przywracanie kopii zapasowej bazy danych na serwerze docelowym z kontenera magazynu w regionie serwera źródłowego. W niektórych przypadkach przywracanie kopii zapasowych z innego regionu może mieć niską wydajność, szczególnie w przypadku dużych baz danych. Aby uzyskać najlepszą wydajność podczas przywracania bazy danych, zmigruj lub utwórz nowy kontener magazynu w regionie serwera docelowego. Skopiuj pliki kopii zapasowej abf z kontenera magazynu w regionie źródłowym do kontenera magazynu w regionie docelowym przed przywróceniem bazy danych na serwerze docelowym. Poza zakresem tego artykułu, w niektórych przypadkach, szczególnie w przypadku bardzo dużych baz danych, wykonywanie skryptów bazy danych z serwera źródłowego, ponowne tworzenie, a następnie przetwarzanie na serwerze docelowym w celu załadowania danych bazy danych może być bardziej ekonomiczne niż użycie kopii zapasowej/przywracania.

> [!NOTE]
> Jeśli do nawiązywania połączenia ze źródłami danych używasz lokalnej bramy danych, musisz również przenieść zasób bramy do regionu serwera docelowego. Aby dowiedzieć się więcej, [zobacz Instalowanie i konfigurowanie lokalnej bramy danych.](analysis-services-gateway-install.md)

## <a name="prerequisites"></a>Wymagania wstępne

- **Konto usługi Azure Storage:** wymagane do przechowywania pliku kopii zapasowej abf.
- **SQL Server Management Studio (SSMS):** wymagane do tworzenia kopii zapasowych i przywracania baz danych modelu.
- Zainstalowanie programu **Azure PowerShell**. Wymagane tylko wtedy, gdy zdecydujesz się wykonać to zadanie przy użyciu programu PowerShell.

## <a name="prepare"></a>Przygotowywanie

### <a name="backup-model-databases"></a>Tworzenie kopii zapasowych baz danych modelu

Jeśli **ustawienia magazynu** nie zostały jeszcze skonfigurowane dla serwera źródłowego, wykonaj kroki opisane w te [tematu Konfigurowanie ustawień magazynu.](analysis-services-backup.md#configure-storage-settings)

Po skonfigurowaniu ustawień magazynu wykonaj kroki opisane w te tematu [Backup](analysis-services-backup.md#backup) (Kopia zapasowa), aby utworzyć kopię zapasową modelu bazy danych abf w kontenerze magazynu. Później przywrócisz kopię zapasową abf na nowy serwer docelowy.


### <a name="export-template"></a>Eksportowanie szablonu

Szablon zawiera właściwości konfiguracji serwera źródłowego.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wyeksportować szablon przy użyciu witryny Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **pozycję Wszystkie** zasoby, a następnie wybierz Analysis Services serwer.

3. Wybierz > **ustawienia eksportu**  >  **szablonu**.

4. Wybierz **pozycję Pobierz** w bloku **Eksportuj** szablon.

5. Znajdź plik zip pobrany z portalu, a następnie rozpakować ten plik do folderu.

   Plik zip zawiera pliki JSON, które składają się na szablon, oraz parametry niezbędne do wdrożenia nowego serwera.


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyeksportować szablon przy użyciu programu PowerShell:

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) i postępuj zgodnie z instrukcjami na ekranie:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Jeśli twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję zasobu serwera, który chcesz przenieść.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Wyeksportuj szablon serwera źródłowego. Te polecenia zapisują szablon JSON z parametrem ResourceGroupName jako nazwą pliku w bieżącym katalogu.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Uzyskiwanie sygnatury dostępu współdzielonego (SAS) magazynu

Podczas wdrażania serwera docelowego z szablonu token SAS delegowania użytkownika (jako identyfikator URI) jest wymagany do określenia kontenera magazynu zawierającego kopię zapasową bazy danych.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać sygnaturę dostępu współdzielonych przy użyciu portalu:

1. W portalu wybierz konto magazynu używane do tworzenia kopii zapasowej bazy danych serwera.

2. Wybierz **pozycję Eksplorator usługi Storage**, a następnie rozwiń pozycję **KONTENERY OBIEKTÓW BLOB.** 

3. Kliknij prawym przyciskiem myszy kontener magazynu, a następnie wybierz polecenie **Pobierz sygnaturę dostępu współdzielonych.**

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="Pobieranie sygnatury dostępu współdzielonego":::

4. W **sygnaturze dostępu współdzielowego** wybierz pozycję **Utwórz.** Domyślnie sygnatura dostępu współdzielonego wygaśnie za 24 godziny.

5. Skopiuj i zapisz **ten plik URI.** 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uzyskać sygnaturę dostępu współdzielonego przy użyciu programu PowerShell, wykonaj kroki opisane w te tematu Create a user delegation SAS for a container or blob with PowerShell (Tworzenie sygnatury dostępu współdzielonego delegowania użytkownika dla kontenera lub obiektu blob za [pomocą programu PowerShell).](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob)

---

### <a name="modify-the-template"></a>Modyfikowanie szablonu

Za pomocą edytora tekstów zmodyfikuj template.jswyeksportowanego pliku, zmieniając właściwości regionu i kontenera obiektów blob. 

Aby zmodyfikować szablon:

1. W edytorze tekstów we właściwości **location** określ nowy region docelowy. We właściwości **backupBlobContainerUri** wklej wartość URI kontenera magazynu z kluczem sygnatury dostępu współdzielonego. 

    W poniższym przykładzie określono region docelowy dla serwera advworks1 i określono wartość URI kontenera magazynu z `South Central US` sygnaturą dostępu współdzielonych: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Zapisz szablon.

#### <a name="regions"></a>Regiony

Aby uzyskać informacje o regionach świadczenia usługi Azure, [zobacz Lokalizacje platformy Azure.](https://azure.microsoft.com/global-infrastructure/locations/) Aby uzyskać regiony przy użyciu programu PowerShell, uruchom [polecenie Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Move

Aby wdrożyć nowy zasób serwera w innym regionie, należy użyćtemplate.js **wyeksportowanego** i zmodyfikowanego w poprzednich sekcjach.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W portalu wybierz pozycję **Utwórz zasób.**

2. W **polu Wyszukaj w witrynie Marketplace** wpisz wdrożenie **szablonu**, a następnie naciśnij klawisz **ENTER.**

3. Wybierz **Template deployment**.

4. Wybierz przycisk **Utwórz**.

5. Wybierz **pozycję Build your own template in the editor (Kompilowanie własnego szablonu w edytorze).**

6. Wybierz **pozycję Załaduj** plik , a następnie postępuj zgodnie z instrukcjami,template.js **załadować** plik wyeksportowany i zmodyfikowany.

7. Sprawdź, czy w edytorze szablonów są poprawne właściwości nowego serwera docelowego.

8. Wybierz pozycję **Zapisz**.

9. Wprowadź lub wybierz wartości właściwości:

    - **Subscription** (Subskrypcja): wybierz subskrypcję platformy Azure.
    
    - **Grupa zasobów:** wybierz **pozycję Utwórz nową,** a następnie wprowadź nazwę grupy zasobów. Możesz wybrać istniejącą grupę zasobów, pod warunkiem, że nie zawiera ona Analysis Services o tej samej nazwie.
    
    - **Lokalizacja:** wybierz ten sam region, który został określony w szablonie.

10. Wybierz **pozycję Przejrzyj i utwórz.**

11. Przejrzyj warunki i podstawy, a następnie wybierz pozycję **Utwórz.**

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj tych poleceń, aby wdrożyć szablon:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Uzyskiwanie wartości URI serwera docelowego

Aby nawiązać połączenie z nowym serwerem docelowym z programu SSMS w celu przywrócenia bazy danych modelu, należy uzyskać nowy docelowy serwer URI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać serwer URI w portalu:

1. W portalu przejdź do nowego zasobu serwera docelowego.

2. Na stronie **Przegląd** skopiuj adres **URI nazwy** serwera.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uzyskać serwerowy kod URI przy użyciu programu PowerShell, użyj następujących poleceń:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Skopiuj **parametr ServerFullName** z danych wyjściowych.

---

### <a name="restore-model-database"></a>Przywracanie bazy danych modelu

Wykonaj kroki opisane w [przywróceniu,](analysis-services-backup.md#restore) aby przywrócić kopię zapasową abf modelowej bazy danych na nowy serwer docelowy.

Opcjonalnie: po przywróceniu bazy danych modelu przetwierd model i tabele, aby odświeżyć dane ze źródeł danych. Aby przetworzyć model i tabelę przy użyciu programu SSMS:

1. W programie SSMS kliknij prawym przyciskiem myszy modelową bazę danych > **przetwarzania.**

2. Rozwiń **pozycję Tabele**, kliknij prawym przyciskiem myszy tabelę. W **tabelach procesów** zaznacz wszystkie tabele, a następnie wybierz przycisk **OK.**

## <a name="verify"></a>Weryfikacja

1. W portalu przejdź do nowego serwera docelowego.

2. Na stronie Przegląd na stronie **Modele na Analysis Services sprawdź,** czy są wyświetlane przywrócone modele.

3. Użyj aplikacji klienckiej, Power BI lub Excel, aby nawiązać połączenie z modelem na nowym serwerze. Sprawdź, czy są wyświetlane obiekty modelu, takie jak tabele, miary i hierarchie. 

4. Uruchamianie skryptów automatyzacji. Sprawdź, czy zostały wykonane pomyślnie.

Opcjonalnie: [ALM Toolkit](http://alm-toolkit.com/) to open source do porównywania zestawów danych i  Power BI danych oraz Analysis Services baz danych modeli tabelaracyjnych.  Użyj zestawu narzędzi, aby nawiązać połączenie z bazami danych serwera źródłowego i docelowego oraz porównać. Jeśli migracja bazy danych powiedzie się, obiekty modelu będą tej samej definicji. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po sprawdzeniu, czy aplikacje klienckie mogą łączyć się z nowym serwerem i wszystkie skrypty automatyzacji są wykonywane poprawnie, usuń serwer źródłowy. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć serwer źródłowy z portalu:

Na stronie Przegląd serwera **źródłowego** wybierz pozycję **Usuń**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby usunąć serwer źródłowy przy użyciu programu PowerShell, użyj Remove-AzAnalysisServicesServer polecenia .

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Po zakończeniu przenoszenia regionu zaleca się, aby nowy serwer docelowy używał kontenera magazynu w tym samym regionie do tworzenia kopii zapasowych, a nie kontenera magazynu w regionie serwera źródłowego.

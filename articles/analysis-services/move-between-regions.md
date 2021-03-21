---
title: Przenieś Azure Analysis Services do innego regionu | Microsoft Docs
description: Opisuje sposób przenoszenia zasobu Azure Analysis Services do innego regionu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 049ff6d14c3967481eb73037814082fa261154e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96497932"
---
# <a name="move-analysis-services-to-a-different-region"></a>Przenieś Analysis Services do innego regionu

W tym artykule opisano sposób przenoszenia zasobu serwera Analysis Services do innego regionu platformy Azure. Serwer można przenieść do innego regionu z wielu powodów, na przykład w celu skorzystania z regionu platformy Azure bliżej użytkowników, korzystania z planów usług obsługiwanych tylko w określonych regionach lub do spełnienia wymagań wewnętrznych i zarządzania. 

W tym i skojarzonym artykule dowiesz się, jak:

> [!div class="checklist"]
> 
> * Tworzenie kopii zapasowej bazy danych modelu serwera źródłowego do [magazynu obiektów BLOB](../storage/blobs/storage-blobs-introduction.md).
> * Wyeksportuj [szablon zasobu](../azure-resource-manager/templates/overview.md)serwera źródłowego.
> * Uzyskaj [sygnaturę dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md)magazynu.
> * Zmodyfikuj szablon zasobu.
> * Wdróż szablon, aby utworzyć nowy serwer docelowy.
> * Przywróć modelową bazę danych na nowy serwer docelowy.
> * Sprawdź nowy serwer docelowy i bazę danych.
> * Usuń serwer źródłowy.

W tym artykule opisano użycie szablonu zasobu do migracji pojedynczego serwera Analysis Services z **podstawową konfiguracją** do innego regionu *i* grupy zasobów w ramach tej samej subskrypcji. Użycie szablonu zachowuje skonfigurowane właściwości serwera, co zapewnia, że serwer docelowy jest skonfigurowany z tymi samymi właściwościami, z wyjątkiem regionu i grupy zasobów jako serwera źródłowego. W tym artykule nie opisano przeniesienia skojarzonych zasobów, które mogą być częścią tej samej grupy zasobów, na przykład źródła danych, magazynu i zasobów bramy. 

Przed przeniesieniem serwera do innego regionu zaleca się utworzenie planu szczegółowego. Należy rozważyć dodatkowe zasoby, takie jak bramy i magazyn, które mogą być również konieczne do przeniesienia. W przypadku każdego planu ważne jest wykonanie co najmniej jednej próbnej operacji przenoszenia przy użyciu serwerów testowych przed przeniesieniem serwera produkcyjnego.

> [!IMPORTANT]
> Aplikacje klienckie i parametry połączenia nawiązują połączenie z Analysis Services przy użyciu pełnej nazwy serwera, która jest identyfikatorem URI zawierającym region, w którym znajduje się serwer. Na przykład `asazure://westcentralus.asazure.windows.net/advworks01`. Podczas przesuwania serwera do innego regionu, możesz efektywnie utworzyć nowy zasób serwera w innym regionie, który będzie mieć inny region w identyfikatorze URI nazwy serwera. Aplikacje klienckie i parametry połączenia używane w skryptach muszą łączyć się z nowym serwerem przy użyciu nowego identyfikatora URI nazwy serwera. Użycie [aliasu nazwy serwera](analysis-services-server-alias.md) może zmniejszyć liczbę miejsc, w których należy zmienić identyfikator URI nazwy serwera, ale muszą one zostać zaimplementowane przed przeniesieniem regionu.

> [!IMPORTANT]
> Regiony platformy Azure używają różnych zakresów adresów IP. W przypadku skonfigurowania wyjątków zapory dla regionu, w którym znajduje się serwer i/lub konto magazynu, może być konieczne skonfigurowanie innego zakresu adresów IP. Aby dowiedzieć się więcej, zobacz [często zadawane pytania dotyczące Analysis Services łączności sieciowej](analysis-services-network-faq.md).

> [!NOTE]
> W tym artykule opisano przywracanie kopii zapasowej bazy danych na serwerze docelowym z kontenera magazynu w regionie serwera źródłowego. W niektórych przypadkach przywracanie kopii zapasowych z innego regionu może mieć niską wydajność, szczególnie w przypadku dużych baz danych. Aby uzyskać najlepszą wydajność podczas przywracania bazy danych, należy przeprowadzić migrację lub utworzyć nowy kontener magazynu w regionie serwera docelowego. Skopiuj pliki kopii zapasowej. abf z kontenera magazynu regionów źródłowych do kontenera magazynu regionu docelowego przed przywróceniem bazy danych na serwerze docelowym. Poza zakresem tego artykułu, w niektórych przypadkach, szczególnie w przypadku bardzo dużych baz danych, skryptuje bazę danych z serwera źródłowego, ponowne utworzenie i przetwarzanie na serwerze docelowym w celu załadowania danych bazy danych może być bardziej opłacalne niż korzystanie z kopii zapasowej/przywracania.

> [!NOTE]
> W przypadku korzystania z lokalnej bramy danych do łączenia się ze źródłami danych należy również przenieść zasób bramy do docelowego obszaru serwera. Aby dowiedzieć się więcej, zobacz [Instalowanie i Konfigurowanie lokalnej bramy danych](analysis-services-gateway-install.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Konto usługi Azure Storage**: wymagane do przechowywania pliku kopii zapasowej. abf.
- **SQL Server Management Studio (SSMS)**: wymagane do tworzenia kopii zapasowych i przywracania baz danych modeli.
- Zainstalowanie programu **Azure PowerShell**. Wymagane tylko w przypadku wybrania tego zadania przy użyciu programu PowerShell.

## <a name="prepare"></a>Przygotowywanie

### <a name="backup-model-databases"></a>Tworzenie kopii zapasowych baz danych modelu

Jeśli **ustawienia magazynu** nie zostały jeszcze skonfigurowane dla serwera źródłowego, wykonaj kroki opisane w sekcji [Konfigurowanie ustawień magazynu](analysis-services-backup.md#configure-storage-settings).

Po skonfigurowaniu ustawień magazynu postępuj zgodnie z instrukcjami w obszarze [kopia zapasowa](analysis-services-backup.md#backup) , aby utworzyć model Database. abf Backup w kontenerze magazynu. Później można przywrócić kopię zapasową. abf na nowym serwerze docelowym.


### <a name="export-template"></a>Eksportowanie szablonu

Szablon zawiera właściwości konfiguracji serwera źródłowego.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wyeksportować szablon przy użyciu witryny Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **wszystkie zasoby**, a następnie wybierz serwer Analysis Services.

3. Wybierz pozycję > **Ustawienia**  >  **Eksportuj szablon**.

4. Wybierz pozycję **Pobierz** w bloku **Eksportuj szablon** .

5. Znajdź plik zip pobrany z portalu, a następnie Rozpakuj ten plik do folderu.

   Plik zip zawiera pliki. JSON, które składają się na szablon i parametry niezbędne do wdrożenia nowego serwera.


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyeksportować szablon przy użyciu programu PowerShell:

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję zasobu serwera, który chcesz przenieść.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Wyeksportuj szablon serwera źródłowego. Te polecenia zapisują szablon JSON z ResourceGroupName jako filename w bieżącym katalogu.

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

W przypadku wdrażania serwera docelowego przy użyciu szablonu token sygnatury dostępu współdzielonego (jako identyfikator URI) jest wymagany do określenia kontenera magazynu zawierającego kopię zapasową bazy danych.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać sygnaturę dostępu współdzielonego przy użyciu portalu:

1. W portalu wybierz konto magazynu używane do tworzenia kopii zapasowej bazy danych serwera.

2. Wybierz pozycję **Eksplorator usługi Storage**, a następnie rozwiń węzeł **kontenery obiektów BLOB**. 

3. Kliknij prawym przyciskiem myszy kontener magazynu, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="Pobieranie sygnatury dostępu współdzielonego":::

4. W obszarze **sygnatura dostępu współdzielonego** wybierz pozycję **Utwórz**. Domyślnie sygnatura dostępu współdzielonego wygaśnie w ciągu 24 godzin.

5. Skopiuj i Zapisz **Identyfikator URI**. 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uzyskać sygnaturę dostępu współdzielonego za pomocą programu PowerShell, wykonaj kroki opisane w temacie [Tworzenie skojarzenia zabezpieczeń delegowania użytkowników dla kontenera lub obiektu BLOB za pomocą programu PowerShell](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob).

---

### <a name="modify-the-template"></a>Modyfikowanie szablonu

Użyj edytora tekstów, aby zmodyfikować template.jseksportowanego pliku, zmieniając właściwości obszaru i kontenera obiektów BLOB. 

Aby zmodyfikować szablon:

1. W edytorze tekstów w polu Właściwość **Location (lokalizacja** ) określ nowy region docelowy. We właściwości **backupBlobContainerUri** wklej identyfikator URI kontenera magazynu z kluczem sygnatury dostępu współdzielonego. 

    Poniższy przykład ustawia region docelowy dla serwera advworks1 na `South Central US` i określa identyfikator URI kontenera magazynu z sygnaturą dostępu współdzielonego: 

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

Aby uzyskać regiony platformy Azure, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Aby uzyskać regiony przy użyciu programu PowerShell, uruchom polecenie [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Move

Aby wdrożyć nowy zasób serwera w innym regionie, użyjesz **template.jsna** wyeksportowanym i zmodyfikowanym pliku w poprzednich sekcjach.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W portalu wybierz pozycję **Utwórz zasób**.

2. W obszarze **Wyszukaj w portalu Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **Enter**.

3. Wybierz **Template Deployment**.

4. Wybierz przycisk **Utwórz**.

5. Wybierz opcję **Kompiluj własny szablon w edytorze**.

6. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować **template.js** eksportowanego i zmodyfikowanego pliku.

7. Sprawdź, czy w edytorze szablonów są wyświetlane poprawne właściwości nowego serwera docelowego.

8. Wybierz pozycję **Zapisz**.

9. Wprowadź lub wybierz wartości właściwości:

    - **Subscription** (Subskrypcja): wybierz subskrypcję platformy Azure.
    
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. Możesz wybrać istniejącą grupę zasobów, pod warunkiem, że nie zawiera jeszcze Analysis Services serwera o tej samej nazwie.
    
    - **Lokalizacja**: Wybierz ten sam region, który został określony w szablonie.

10. Wybierz pozycję **Przejrzyj i Utwórz**.

11. Zapoznaj się z warunkami i podstawami, a następnie wybierz pozycję **Utwórz**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj tych poleceń do wdrożenia szablonu:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Pobierz identyfikator URI serwera docelowego

Aby można było połączyć się z nowym serwerem docelowym z programu SSMS w celu przywrócenia bazy danych modelu, należy uzyskać nowy identyfikator URI serwera docelowego.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać identyfikator URI serwera w portalu:

1. W portalu przejdź do nowego zasobu serwera docelowego.

2. Na stronie **Przegląd** Skopiuj identyfikator URI **nazwy serwera** .

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uzyskać identyfikator URI serwera przy użyciu programu PowerShell, użyj następujących poleceń:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Skopiuj **ServerFullName** z danych wyjściowych.

---

### <a name="restore-model-database"></a>Przywracanie bazy danych modelu

Wykonaj kroki opisane w sekcji [przywracanie](analysis-services-backup.md#restore) , aby przywrócić model Database. abf kopii zapasowej na nowym serwerze docelowym.

Opcjonalne: po przywróceniu bazy danych modelu przetwórz model i tabele, aby odświeżyć dane ze źródeł danych. Aby przetworzyć model i tabelę za pomocą programu SSMS:

1. W programie SSMS kliknij prawym przyciskiem myszy bazę danych modelu > **przetwarzania bazy** danych.

2. Rozwiń węzeł **tabele**, kliknij prawym przyciskiem myszy tabelę. W **tabeli procesów** zaznacz opcję Wszystkie tabele, a następnie wybierz przycisk **OK**.

## <a name="verify"></a>Weryfikacja

1. W portalu przejdź do nowego serwera docelowego.

2. Na stronie Przegląd w obszarze **modele na serwerze Analysis Services** Sprawdź, czy przywrócone modele są widoczne.

3. Użyj aplikacji klienckiej, takiej jak Power BI lub Excel, aby nawiązać połączenie z modelem na nowym serwerze. Sprawdź obiekty modelu, takie jak tabele, miary i hierarchie. 

4. Uruchom dowolne skrypty automatyzacji. Sprawdź, czy zostały wykonane pomyślnie.

Opcjonalnie: [Alm Toolkit](http://alm-toolkit.com/) to narzędzie *Open Source* służące do porównywania i zarządzania Power BI zestawami danych *i* Analysis Servicesymi bazami kodu modelu tabelarycznego. Użyj zestawu narzędzi, aby połączyć się z bazami danych serwera źródłowego i docelowego i porównać. Jeśli migracja bazy danych zakończy się pomyślnie, obiekty modelu będą mieć tę samą definicję. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po sprawdzeniu, czy aplikacje klienckie mogą nawiązywać połączenie z nowym serwerem, a wszystkie skrypty automatyzacji są wykonywane prawidłowo, Usuń serwer źródłowy. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć serwer źródłowy z portalu:

Na stronie **Przegląd** serwera źródłowego wybierz pozycję **Usuń**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby usunąć serwer źródłowy przy użyciu programu PowerShell, użyj polecenia Remove-AzAnalysisServicesServer.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Po zakończeniu przenoszenia regionu zaleca się, aby nowy serwer docelowy używał kontenera magazynu w tym samym regionie dla kopii zapasowych, a nie kontenera magazynu w regionie serwera źródłowego.
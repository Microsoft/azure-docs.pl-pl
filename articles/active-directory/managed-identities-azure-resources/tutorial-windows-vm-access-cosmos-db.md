---
title: 'Samouczek: używanie tożsamości zarządzanej do uzyskiwania dostępu do Azure Cosmos DB-Windows-Azure AD'
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b571b2b8e0d334a02631e3f443ec54398117ee9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532673"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Cosmos DB

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób używania tożsamości zarządzanej przypisanej przez system dla maszyny wirtualnej z systemem Windows w celu uzyskania dostępu do usługi Cosmos DB. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie konta usługi Cosmos DB
> * Udzielanie dostępu przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do kluczy dostępu do konta usługi Cosmos DB
> * Uzyskiwanie tokenu dostępu przy użyciu przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do wywołania usługi Azure Resource Manager
> * Uzyskiwanie kluczy dostępu z usługi Azure Resource Manager w celu wykonywania wywołań usługi Cosmos DB

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Zainstalowanie najnowszej wersji programu [Azure PowerShell](/powershell/azure/install-az-ps)


## <a name="enable"></a>Włącz

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Udzielanie dostępu


### <a name="create-a-cosmos-db-account"></a>Tworzenie konta usługi Cosmos DB 

Jeśli jeszcze nie masz konta usługi Cosmos DB, utwórz je. Możesz pominąć ten krok i użyć istniejącego konta usługi Cosmos DB. 

1. Kliknij przycisk **+/Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Kliknij kolejno pozycje **Bazy danych** i **Azure Cosmos DB**. Zostanie wyświetlony panel „Nowe konto”.
3. Wprowadź **identyfikator** konta usługi Cosmos DB do późniejszego użycia.  
4. **Interfejs API** należy ustawić na „SQL”. Podejście opisane w tym samouczku można stosować w przypadku innych dostępnych typów interfejsu API, ale kroki opisane w tym samouczku dotyczą interfejsu API SQL.
5. Upewnij się, że **Subskrypcja** i **Grupa zasobów** pasują do wartości określonych podczas tworzenia maszyny wirtualnej w poprzednim kroku.  Wybierz **lokalizację**, w której jest dostępna usługa Cosmos DB.
6. Kliknij przycisk **Utwórz**.

### <a name="create-a-collection"></a>Tworzenie kolekcji 

Następnie na koncie usługi Cosmos DB dodaj kolekcję danych, dla której możesz utworzyć zapytania w kolejnych krokach.

1. Przejdź do nowo utworzonego konta usługi Cosmos DB.
2. Na karcie **Omówienie** kliknij przycisk **+/Dodaj kolekcję**. Zostanie wysunięty panel „Dodawanie kolekcji”.
3. Nadaj kolekcji identyfikator bazy danych i identyfikator kolekcji, wybierz pojemność magazynu, wprowadź klucz partycji, wprowadź wartość przepływności, a następnie kliknij przycisk **OK**.  W tym samouczku wystarczy użyć identyfikatora bazy danych i identyfikatora kolekcji „Test”, a następnie wybrać stałą pojemność magazynu i najniższą przepływność (400 RU/s).  


### <a name="grant-access-to-the-cosmos-db-account-access-keys"></a>Udzielanie dostępu do kluczy dostępu do konta Cosmos DB

W tej sekcji przedstawiono sposób udzielania dostępu tożsamości zarządzanej przez system Windows VM do kluczy dostępu do konta Cosmos DB. Usługa Cosmos DB nie zapewnia natywnej obsługi uwierzytelniania usługi Azure AD. Możesz jednak użyć przypisanej przez system tożsamości zarządzanej, aby pobrać klucz dostępu do konta usługi Cosmos DB z usługi Resource Manager, a następnie użyć klucza do uzyskania dostępu do usługi Cosmos DB. W tym kroku udzielasz przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows dostępu do kluczy do konta usługi Cosmos DB.

Aby udzielić przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows dostępu do konta usługi Cosmos DB w usłudze Azure Resource Manager przy użyciu programu PowerShell, zaktualizuj wartości `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` i `<COSMOS DB ACCOUNT NAME>` dla swojego środowiska. W przypadku korzystania z kluczy dostępu usługa Cosmos DB obsługuje dwa poziomy szczegółowości: dostęp do odczytu/zapisu na koncie i dostęp tylko do odczytu do konta.  Przypisz rolę `DocumentDB Account Contributor`, jeśli chcesz uzyskać klucze odczytu/zapisu dla konta, lub przypisz rolę `Cosmos DB Account Reader Role`, jeśli chcesz uzyskać klucze tylko do odczytu dla konta.  W tym samouczku przypisz rolę `Cosmos DB Account Reader Role`:

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> Należy pamiętać, że jeśli nie możesz wykonać operacji, możesz nie mieć odpowiednich uprawnień. Jeśli chcesz uzyskać dostęp do zapisu do kluczy, musisz użyć roli RBAC, takiej jak współautor konta DocumentDB lub utworzyć rolę niestandardową. Więcej informacji na temat [kontroli dostępu opartej na rolach znajduje się w Azure Cosmos DB](../../cosmos-db/role-based-access-control.md)

## <a name="access-data"></a>Uzyskiwanie dostępu do danych

W tej sekcji przedstawiono sposób wywoływania Azure Resource Manager przy użyciu tokenu dostępu dla tożsamości zarządzanej przypisanej przez system Windows VM. W pozostałej części tego samouczka będziemy pracować z poziomu wcześniej utworzonej maszyny wirtualnej. 

Musisz zainstalować najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) na maszynie wirtualnej z systemem Windows.



### <a name="get-an-access-token"></a>Pobranie tokenu dostępu

1. W witrynie Azure Portal przejdź do pozycji **Maszyny wirtualne**, przejdź do maszyny wirtualnej z systemem Windows, a następnie na stronie **Przegląd** kliknij opcję **Połącz** u góry. 
2. Wprowadź **nazwę użytkownika** i **hasło** dodane podczas tworzenia maszyny wirtualnej z systemem Windows. 
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program PowerShell w sesji zdalnej.
4. Używając polecenia Invoke-WebRequest programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanych dla zasobów platformy Azure, aby uzyskać token dostępu na potrzeby usługi Azure Resource Manager.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > Wartość parametru „resource” musi być dokładnie dopasowana do tego, czego oczekujemy od usługi Azure AD. W przypadku użycia identyfikatora zasobu usługi Azure Resource Manager należy uwzględnić końcowy ukośnik w identyfikatorze URI.
    
   Następnie wyodrębnij element„Content”, który jest przechowywany w ciągu w formacie JavaScript Object Notation (JSON) w obiekcie $response. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   Następnie wyodrębnij token dostępu z odpowiedzi.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>Pobierz klucze dostępu 

W tej sekcji pokazano, jak uzyskać klucze dostępu z Azure Resource Manager, aby wykonać Cosmos DB wywołania. Teraz użyj programu PowerShell, aby wywołać usługę Resource Manager przy użyciu tokenu dostępu pozyskanego w poprzedniej sekcji, aby pobrać klucz dostępu do usługi Cosmos DB. Gdy będziemy już mieć klucz dostępu, będzie można wykonać zapytanie dotyczące usługi Cosmos DB. Pamiętaj, aby zastąpić parametry `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` i `<COSMOS DB ACCOUNT NAME>` własnymi wartościami. Zastąp wartość `<ACCESS TOKEN>` tokenem dostępu pobranym wcześniej.  Jeśli chcesz pobrać klucze odczytu/zapisu, użyj typu operacji klucza `listKeys`.  Jeśli chcesz pobrać klucze tylko do odczytu, użyj typu operacji klucza `readonlykeys`:

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
Odpowiedź zapewnia listę kluczy.  Jeśli pobierasz klucze tylko do odczytu:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Teraz, gdy masz klucz dostępu do konta usługi Cosmos DB, możesz przekazać go do zestawu SDK usługi Cosmos DB i wykonywać wywołania w celu uzyskania dostępu do konta.  Szybki przykład to przekazanie klucza dostępu do wiersza polecenia platformy Azure.  Element `<COSMOS DB CONNECTION URL>` można uzyskać na karcie **Omówienie** karty w bloku konta usługi Cosmos DB w witrynie Azure Portal.  Zastąp element wartością `<ACCESS KEY>` uzyskaną powyżej:

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

To polecenie interfejsu wiersza polecenia zwraca szczegółowe informacje o kolekcji:

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```


## <a name="disable"></a>Wyłącz

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób używania przypisanej przez system tożsamości maszyny wirtualnej z systemem Windows w celu uzyskania dostępu do usługi Cosmos DB.  Aby dowiedzieć się więcej o usłudze Cosmos DB, zobacz:

> [!div class="nextstepaction"]
>[Przegląd Azure Cosmos DB](../../cosmos-db/introduction.md)
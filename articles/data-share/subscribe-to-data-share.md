---
title: 'Samouczek: akceptowanie & odbierania danych — udział danych platformy Azure'
description: Samouczek — akceptowanie i odbieranie danych za pomocą udziału danych platformy Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: a225989f0670e9b62b00a35bac719c9357c8a130
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017053"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Samouczek: Akceptowanie i odbieranie danych przy użyciu usługi Azure Data Share  

W tym samouczku dowiesz się, jak zaakceptować zaproszenie do udziału danych przy użyciu udziału danych platformy Azure. Dowiesz się, jak odbierać dane udostępniane Tobie, a także jak włączyć okres regularnego odświeżania, aby upewnić się, że zawsze masz najnowszą migawkę danych udostępnionych Tobie. 

> [!div class="checklist"]
> * Jak zaakceptować zaproszenie do udziału danych platformy Azure
> * Tworzenie konta udziału danych platformy Azure
> * Określ miejsce docelowe dla danych
> * Utwórz subskrypcję udziału danych na potrzeby zaplanowanego odświeżania

## <a name="prerequisites"></a>Wymagania wstępne
Przed zaakceptowaniem zaproszenia do udziału danych musisz udostępnić kilka zasobów platformy Azure, które są wymienione poniżej. 

Przed zaakceptowaniem zaproszenia udziału danych upewnij się, że wszystkie wymagania wstępne zostały ukończone. 

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Zaproszenie do udziału danych: zaproszenie od Microsoft Azure z podmiotem zatytułowanym "zaproszenie do udziału danych platformy Azure **<yourdataprovider@domain.com>** ".
* Zarejestruj [dostawcę zasobów Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji platformy Azure, w której utworzysz zasób udziału danych i subskrypcję platformy Azure, w której znajdują się docelowe magazyny danych platformy Azure.

### <a name="receive-data-into-a-storage-account"></a>Odbieranie danych do konta magazynu

* Konto usługi Azure Storage: Jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](../storage/common/storage-account-create.md). 
* Uprawnienie do zapisu na koncie magazynu, które jest obecne w usłudze *Microsoft. Storage/storageAccounts/Write*. To uprawnienie istnieje w roli Współautor. 
* Uprawnienie do dodawania przypisania roli do konta magazynu, które jest obecne w *firmie Microsoft. Autoryzacja/przypisania ról/zapis*. To uprawnienie istnieje w roli Właściciel.  

### <a name="receive-data-into-a-sql-based-target"></a>Odbieranie danych do obiektu docelowego opartego na języku SQL
Jeśli zdecydujesz się na otrzymywanie danych do Azure SQL Database, usługa Azure Synapse Analytics poniżej stanowi listę wymagań wstępnych. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Wymagania wstępne dotyczące otrzymywania danych w usłudze Azure SQL Database lub Azure Synapse Analytics (dawniej: Azure SQL DW)
Aby skonfigurować wymagania wstępne, można wykonać [pokaz krok po kroku](https://youtu.be/aeGISgK1xro) .

* Azure SQL Database lub usługa Azure Synapse Analytics (dawniej usługa Azure SQL DW).
* Uprawnienia do zapisu w bazach danych programu SQL Server, które znajdują się w *Microsoft. SQL/Servers/Databases/Write*. To uprawnienie istnieje w roli **Współautor**. 
* Uprawnienie do zarządzanej tożsamości zasobu udziału danych w celu uzyskania dostępu do Azure SQL Database lub analizy usługi Azure Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do serwera SQL i ustaw go jako **administratora Azure Active Directory**.
    1. Nawiązywanie połączenia z usługą Azure SQL Database/magazynem danych przy użyciu [edytora zapytań](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) lub SQL Server Management Studio z uwierzytelnianiem Azure Active Directory. 
    1. Wykonaj następujący skrypt, aby dodać tożsamość zarządzaną udziału danych jako "db_datareader, db_datawriter, db_ddladmin". Musisz nawiązać połączenie przy użyciu Active Directory, a nie SQL Server uwierzytelniania. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Należy pamiętać, że *<share_acc_name>* to nazwa zasobu udziału danych. Jeśli zasób udział danych nie został jeszcze utworzony, możesz wrócić do tego wymagania wstępnego później.         

* SQL Server dostęp do zapory. Można to zrobić, wykonując następujące czynności: 
    1. W programie SQL Server w Azure Portal przejdź do *zapór i sieci wirtualnych*
    1. Kliknij  przycisk tak *, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego serwera*.
    1. Kliknij pozycję **+ Dodaj adres IP klienta**. Adres IP klienta może ulec zmianie. Ten proces może wymagać powtarzania przy następnym udostępnieniu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Wymagania wstępne dotyczące otrzymywania danych w puli SQL usługi Azure Synapse Analytics (Workspace)

* Dedykowana Pula SQL usługi Azure Synapse Analytics (Workspace). Otrzymywanie danych do puli SQL bez serwera nie jest obecnie obsługiwane.
* Uprawnienia do zapisu w puli SQL w obszarze roboczym Synapse, które znajdują się w *witrynie Microsoft. Synapse/Workspaces/Sqlpools/Write*. To uprawnienie istnieje w roli **Współautor**.
* Uprawnienie do zarządzanej tożsamości zasobu udziału danych w celu uzyskania dostępu do puli SQL obszaru roboczego Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do obszaru roboczego Synapse. Wybierz pozycję SQL Active Directory administrator na lewym panelu nawigacyjnym i ustaw siebie jako **administratora Azure Active Directory**.
    1. Otwórz program Synapse Studio, wybierz pozycję *Zarządzaj* w lewym obszarze nawigacji. Wybierz pozycję *Kontrola dostępu* w obszarze zabezpieczenia. Przypisz siebie do roli administratora **SQL** lub **obszaru roboczego** .
    1. W programie Synapse Studio wybierz pozycję *programowanie* na lewym pasku nawigacyjnym. Wykonaj następujący skrypt w puli SQL, aby dodać tożsamość zarządzaną zasobu udziału danych jako "db_datareader, db_datawriter, db_ddladmin". 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Należy pamiętać, że *<share_acc_name>* to nazwa zasobu udziału danych. Jeśli zasób udział danych nie został jeszcze utworzony, możesz wrócić do tego wymagania wstępnego później.  

* Dostęp do zapory obszaru roboczego Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do obszaru roboczego Synapse. Wybierz pozycję *zapory* na lewym pasku nawigacyjnym.
    1. Kliknij  pozycję Włącz *, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego obszaru roboczego*.
    1. Kliknij pozycję **+ Dodaj adres IP klienta**. Adres IP klienta może ulec zmianie. Ten proces może wymagać powtarzania przy następnym udostępnieniu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Odbieraj dane w klastrze Eksplorator danych platformy Azure: 

* Klaster usługi Azure Eksplorator danych w tym samym centrum danych platformy Azure co klaster Eksplorator danych dostawcy danych: Jeśli jeszcze tego nie masz, możesz utworzyć [klaster Eksplorator danych platformy Azure](/azure/data-explorer/create-cluster-database-portal). Jeśli nie znasz centrum danych platformy Azure w klastrze dostawcy danych, możesz utworzyć klaster w dalszej części procesu.
* Uprawnienie do zapisu w klastrze usługi Azure Eksplorator danych, który znajduje się w *witrynie Microsoft. Kusto/klastrów/Write*. To uprawnienie istnieje w roli Współautor. 
* Uprawnienie do dodawania przypisania roli do klastra usługi Azure Eksplorator danych, który jest obecny w *firmie Microsoft. Autoryzacja/przypisania ról/zapis*. To uprawnienie istnieje w roli Właściciel. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otwórz zaproszenie

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Możesz otworzyć zaproszenie z poczty e-mail lub bezpośrednio z Azure Portal. 

   Aby otworzyć zaproszenie z poczty e-mail, sprawdź skrzynkę odbiorczą dla zaproszenia od dostawcy danych. Zaproszenie pochodzi z Microsoft Azure, zatytułowane **zaproszenie <yourdataprovider@domain.com> udziału danych platformy Azure**. Kliknij pozycję **Wyświetl zaproszenie** , aby zobaczyć zaproszenie na platformie Azure. 

   Aby otworzyć zaproszenie z Azure Portal bezpośrednio, Wyszukaj **zaproszenia udziału danych** w Azure Portal. Ta akcja spowoduje przejście do listy zaproszeń udziału danych.

   ![Lista zaproszeń](./media/invitations.png "Lista zaproszeń") 

1. Wybierz udział, który chcesz wyświetlić. 

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Przygotuj środowisko interfejsu wiersza polecenia platformy Azure, a następnie Wyświetl zaproszenia.

Rozpocznij od przygotowania środowiska dla interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Uruchom polecenie [AZ datashare zaproszenia użytkownika list](/cli/azure/ext/datashare/datashare/consumer/invitation#ext_datashare_az_datashare_consumer_invitation_list) , aby wyświetlić bieżące zaproszenia:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Skopiuj swój identyfikator zaproszenia do użycia w następnej sekcji.

---

## <a name="accept-invitation"></a>Zaakceptuj zaproszenie

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Upewnij się, że wszystkie pola są przeglądane, w tym **warunki użytkowania**. Jeśli akceptujesz warunki użytkowania, musisz zaznaczyć pole wyboru, aby wskazać, że zgadzasz się. 

   ![Warunki użytkowania](./media/terms-of-use.png "Warunki użytkowania") 

1. W obszarze *docelowe konto udziału danych* wybierz subskrypcję i grupę zasobów, w ramach której będziesz wdrażać udział danych. 

   W polu **konto udostępniania danych** wybierz pozycję **Utwórz nową** , jeśli nie masz istniejącego konta udziału danych. W przeciwnym razie wybierz istniejące konto udziału danych, do którego chcesz zaakceptować udział danych. 

   W polu **Nazwa otrzymanego udziału** można pozostawić wartość domyślną określoną przez dane lub określić nową nazwę dla odebranego udziału. 

   Po uzgodnieniu warunków użytkowania i określeniu konta udziału danych do zarządzania odebranym udziałem wybierz pozycję **Zaakceptuj i skonfiguruj**. Zostanie utworzona subskrypcja udziału. 

   ![Zaakceptuj opcje](./media/accept-options.png "Zaakceptuj opcje") 

   Ta akcja spowoduje przejście do otrzymanego udziału na koncie udziału danych. 

   Jeśli nie chcesz zaakceptować zaproszenia, wybierz pozycję *Odrzuć*. 

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [AZ datashare Consumer Share-Subscription Create](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_create) , aby utworzyć udział danych.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Konfiguruj odebrany udział

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj poniższe kroki, aby skonfigurować miejsce, w którym chcesz otrzymywać dane.

1. Wybierz kartę **zestawy danych** . Zaznacz pole wyboru obok zestawu danych, do którego chcesz przypisać miejsce docelowe. Wybierz pozycję **+ Mapuj, aby** wybrać docelowy magazyn danych. 

   ![Mapuj do elementu docelowego](./media/dataset-map-target.png "Mapuj do elementu docelowego") 

1. Wybierz docelowy typ magazynu danych, w którym chcesz umieścić dane. Wszystkie pliki danych lub tabele w docelowym magazynie danych o tej samej ścieżce i nazwie zostaną zastąpione. 

   W przypadku udostępniania w miejscu wybierz magazyn danych w określonej lokalizacji. Lokalizacja to centrum danych platformy Azure, w którym znajduje się źródłowy magazyn danych dostawcy danych. Po zmapowaniu zestawu danych możesz użyć linku w ścieżce docelowej, aby uzyskać dostęp do danych.

   ![Docelowe konto magazynu](./media/dataset-map-target-sql.png "Magazyn docelowy") 

1. W przypadku udostępniania opartego na migawce, jeśli dostawca danych utworzył harmonogram migawek w celu zapewnienia regularnej aktualizacji danych, można również włączyć harmonogram migawek, wybierając kartę **harmonogram migawek** . Zaznacz pole wyboru obok harmonogramu migawek i wybierz pozycję **+ Włącz**.

   ![Włącz harmonogram migawek](./media/enable-snapshot-schedule.png "Włącz harmonogram migawek")

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Te polecenia służą do konfigurowania miejsca, w którym mają być odbierane dane.

1. Uruchom polecenie [AZ datashare Consumer Share-Subscription-DataSet](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_dataset) , aby uzyskać identyfikator zestawu danych:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Uruchom polecenie [AZ Storage account Create](/cli/azure/storage/account#az_storage_account_create) , aby utworzyć konto magazynu dla tego udziału danych:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Aby uzyskać identyfikator konta magazynu, użyj polecenia [AZ Storage account show](/cli/azure/storage/account#az_storage_account_show) :

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Użyj następującego polecenia, aby uzyskać identyfikator podmiotu zabezpieczeń konta:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Użyj polecenia [AZ role przypisanie Create](/cli/azure/role/assignment#az_role_assignment_create) , aby utworzyć przypisanie roli dla podmiotu zabezpieczeń konta:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Utwórz zmienną dla mapowania opartą na IDENTYFIKATORze zestawu danych:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Użyj polecenia [AZ datashare Consumer DataSet-Mapping Create](/cli/azure/ext/datashare/datashare/consumer/dataset-mapping#ext_datashare_az_datashare_consumer_dataset_mapping_create) , aby utworzyć mapowanie zestawu danych:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Uruchom polecenie [AZ datashare Consumer Share-Subscription Synchronize Start](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_start) , aby rozpocząć synchronizację zestawu danych.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Uruchom polecenie [AZ datashare Consumer Share-Subscription list synchronizacji](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_list) , aby wyświetlić listę Twoich synchronizacji:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Aby wyświetlić ustawienia synchronizacji ustawione w udziale, użyj polecenia [AZ datashare Consumer Share-Subscription-Source-Synchronization-Setting](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) .

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Wyzwalanie migawki

### <a name="portal"></a>[Portal](#tab/azure-portal)

Te kroki dotyczą tylko udostępniania opartego na migawce.

1. Możesz wyzwolić migawkę, wybierając kartę **szczegóły** , a następnie pozycję **Wyzwalaj migawkę**. Tutaj można wyzwolić pełną lub przyrostową migawkę danych. Jeśli po raz pierwszy otrzymujesz dane od dostawcy danych, wybierz pozycję pełna kopia. 

   ![Wyzwalanie migawki](./media/trigger-snapshot.png "Wyzwalanie migawki") 

1. Po *pomyślnym* zakończeniu ostatniego uruchomienia Przejdź do docelowego magazynu danych, aby wyświetlić odebrane dane. Wybierz pozycję **zestawy danych**, a następnie kliknij link w ścieżce docelowej. 

   ![Zestawy danych dla odbiorców](./media/consumer-datasets.png "Mapowanie zestawu danych klienta") 

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Uruchom polecenie [AZ datashare Consumer Trigger Create](/cli/azure/ext/datashare/datashare/consumer/trigger#ext_datashare_az_datashare_consumer_trigger_create) , aby wyzwolić migawkę:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Użyj tego polecenia tylko dla udostępniania opartego na migawce.

---

## <a name="view-history"></a>Wyświetlanie historii
Ten krok dotyczy tylko udostępniania opartego na migawce. Aby wyświetlić historię migawek, wybierz pozycję Karta **historia** . W tym miejscu znajdziesz historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 30 dni.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasób nie jest już wymagany, przejdź do strony **Przegląd udziału danych** i wybierz pozycję **Usuń** , aby go usunąć.

## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka nauczysz się akceptować i odbierać udział danych platformy Azure. Aby dowiedzieć się więcej na temat pojęć związanych z udostępnianiem danych platformy Azure, przejdź do terminologii dotyczącej udostępniania danych platformy Azure.

> [!div class="nextstepaction"]
> [Pojęcia dotyczące udziałów danych platformy Azure](terminology.md)
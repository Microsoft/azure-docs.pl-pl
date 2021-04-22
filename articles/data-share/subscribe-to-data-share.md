---
title: 'Samouczek: akceptowanie & odbierania danych — Azure Data Share'
description: Samouczek — akceptowanie i odbieranie danych przy użyciu Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: d063a0870616b5b977df18c56d9d66515b03d0a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870863"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Samouczek: Akceptowanie i odbieranie danych przy użyciu usługi Azure Data Share  

Z tego samouczka dowiesz się, jak zaakceptować zaproszenie do udostępniania danych przy użyciu Azure Data Share. Dowiesz się, jak odbierać dane udostępniane, a także jak włączyć regularny interwał odświeżania, aby mieć pewność, że zawsze masz najnowszą migawkę danych, która jest Ci udostępniana. 

> [!div class="checklist"]
> * Jak zaakceptować zaproszenie Azure Data Share
> * Tworzenie Azure Data Share konta
> * Określanie miejsca docelowego dla danych
> * Tworzenie subskrypcji udziału danych w celu zaplanowanego odświeżania

## <a name="prerequisites"></a>Wymagania wstępne
Zanim będzie można zaakceptować zaproszenie do udostępniania danych, należy aprowizć kilka zasobów platformy Azure wymienionych poniżej. 

Przed zaakceptowaniem zaproszenia do udostępniania danych upewnij się, że wszystkie wymagania wstępne zostały ukończone. 

* Subskrypcja platformy Azure: jeśli nie masz subskrypcji platformy Azure, przed [rozpoczęciem](https://azure.microsoft.com/free/) utwórz bezpłatne konto.
* Zaproszenie Data Share: zaproszenie od Microsoft Azure z tematem zatytułowanym "Azure Data Share zaproszenie z **<yourdataprovider@domain.com>** ".
* Zarejestruj dostawcę [zasobów Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji platformy Azure, w której utworzysz zasób usługi Data Share i subskrypcję platformy Azure, w której znajdują się docelowe magazyny danych platformy Azure.

### <a name="receive-data-into-a-storage-account"></a>Odbieranie danych na konto magazynu

* Konto usługi Azure Storage: jeśli jeszcze go nie masz, możesz utworzyć konto [usługi Azure Storage.](../storage/common/storage-account-create.md) 
* Uprawnienie do zapisu na koncie magazynu, które znajduje się w *microsoft.storage/storageAccounts/write.* To uprawnienie istnieje w roli Współautor. 
* Uprawnienie do dodawania przypisania roli do konta magazynu, które znajduje się w witrynie *Microsoft.Authorization/role assignments/write.* To uprawnienie istnieje w roli Właściciel.  

### <a name="receive-data-into-a-sql-based-target"></a>Odbieranie danych do obiektu docelowego opartego na języku SQL
Jeśli zdecydujesz się odbierać dane do Azure SQL Database, Azure Synapse Analytics, poniżej znajduje się lista wymagań wstępnych. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Wymagania wstępne dotyczące odbierania danych do Azure SQL Database lub Azure Synapse Analytics (wcześniej Azure SQL DW)

* Konto Azure SQL Database lub Azure Synapse Analytics (wcześniej Azure SQL DW).
* Uprawnienie do zapisu w bazach danych na serwerze SQL, które znajduje się w *microsoft.sql/servers/databases/write.* To uprawnienie istnieje w roli **Współautor**. 
* **Azure Active Directory administratora** serwera SQL
* SQL Server dostęp do zapory. W tym celu należy wykonać następujące czynności: 
    1. W programie SQL Server Azure Portal przejdź do *zapór i sieci wirtualnych*
    1. Kliknij **przycisk Tak,** *aby zezwolić usługom i zasobom platformy Azure na dostęp do tego serwera.*
    1. Kliknij **pozycję +Dodaj adres IP klienta.** Adres IP klienta może ulec zmianie. Może być konieczne powtórzenie tego procesu przy następnym udostępnianiu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Wymagania wstępne dotyczące odbierania danych do puli SQL Azure Synapse Analytics (obszar roboczy)

* Dedykowana Azure Synapse Analytics SQL (obszar roboczy). Odbieranie danych do bez serwera puli SQL nie jest obecnie obsługiwane.
* Uprawnienie do zapisu w puli SQL w obszarze roboczym usługi Synapse, który znajduje się w obszarze *Microsoft.Synapse/workspaces/sqlPools/write.* To uprawnienie istnieje w roli **Współautor**.
* Uprawnienie do Data Share zarządzanego zasobu usługi Synapse na dostęp do puli SQL obszaru roboczego usługi Synapse. W tym celu należy wykonać następujące czynności: 
    1. W Azure Portal przejdź do obszaru roboczego synapse. Wybierz pozycję Administrator usługi SQL Active Directory w lewym panelu nawigacyjnym i ustaw siebie jako **Azure Active Directory administratora.**
    1. Otwórz Synapse Studio wybierz pozycję *Zarządzaj w* lewym panelu nawigacyjnym. Wybierz *pozycję Kontrola dostępu w* obszarze Zabezpieczenia. Przypisz sobie **rolę administratora SQL** lub administratora obszaru **roboczego.**
    1. W Synapse Studio wybierz pozycję *Opracuj* w lewym panelu nawigacyjnym. Wykonaj następujący skrypt w puli SQL, aby dodać tożsamość zarządzaną Data Share zasobu jako db_datareader, db_datawriter, db_ddladmin". 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Pamiętaj, *<share_acc_name>* to nazwa zasobu Data Share zasobów. Jeśli jeszcze nie utworzono zasobu Data Share, możesz wrócić do tego wymagania wstępnego później.  

* Dostęp zapory obszaru roboczego usługi Synapse. Można to zrobić za pomocą następujących kroków: 
    1. W Azure Portal przejdź do obszaru roboczego synapse. Wybierz *pozycję Zapory w lewym* panelu nawigacyjnym.
    1. Kliknij **pozycję WŁ.,** *aby zezwolić usługom i zasobom platformy Azure na dostęp do tego obszaru roboczego.*
    1. Kliknij **pozycję +Dodaj adres IP klienta.** Adres IP klienta może ulec zmianie. Może być konieczne powtórzenie tego procesu przy następnym udostępnianiu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Odbieranie danych do klastra Azure Data Explorer klastra: 

* Klaster Azure Data Explorer w tym samym centrum danych platformy Azure co klaster usługi Eksplorator danych dostawcy danych: jeśli jeszcze go nie masz, możesz utworzyć klaster Azure Data Explorer [danych.](/azure/data-explorer/create-cluster-database-portal) Jeśli nie znasz centrum danych platformy Azure klastra dostawcy danych, możesz utworzyć klaster w dalszej części tego procesu.
* Uprawnienie do zapisu w klastrze Azure Data Explorer, który znajduje się w *microsoft.kusto/clusters/write.* To uprawnienie istnieje w roli Współautor. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otwieranie zaproszenia

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaproszenie możesz otworzyć za pomocą wiadomości e-mail lub bezpośrednio z Azure Portal. 

   Aby otworzyć zaproszenie z wiadomości e-mail, sprawdź w skrzynce odbiorczej zaproszenie od dostawcy danych. Zaproszenie pochodzi z Microsoft Azure o tytule Azure Data Share **z <yourdataprovider@domain.com> .** Kliknij pozycję **Wyświetl zaproszenie,** aby wyświetlić zaproszenie na platformie Azure. 

   Aby otworzyć zaproszenie bezpośrednio z Azure Portal, wyszukaj Data Share **zaproszenia w** Azure Portal. Ta akcja przenosi cię do listy Data Share zaproszenia.

   ![Lista zaproszeń](./media/invitations.png "Lista zaproszeń") 

1. Wybierz udział, który chcesz wyświetlić. 

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Przygotuj środowisko interfejsu wiersza polecenia platformy Azure, a następnie wyświetl zaproszenia.

Rozpocznij od przygotowania środowiska dla interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Uruchom polecenie [az datashare consumer invitation list,](/cli/azure/datashare/consumer/invitation#az_datashare_consumer_invitation_list) aby wyświetlić bieżące zaproszenia:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Skopiuj identyfikator zaproszenia do użycia w następnej sekcji.

---

## <a name="accept-invitation"></a>Akceptowanie zaproszenia

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Upewnij się, że wszystkie pola, w tym warunki **użytkowania, są przeglądane.** Jeśli akceptujesz warunki użytkowania, musisz zaznaczyć pole wyboru, aby wskazać, że się zgadzasz. 

   ![Warunki użytkowania](./media/terms-of-use.png "Warunki użytkowania") 

1. W *obszarze Data Share docelowe* wybierz subskrypcję i grupę zasobów, w których będziesz wdrażać Data Share zasobów. 

   W polu **Data Share Konto** wybierz pozycję **Utwórz** nowe, jeśli nie masz istniejącego Data Share konta. W przeciwnym razie wybierz istniejące Data Share, na które chcesz zaakceptować udział danych. 

   W polu **Odebrano nazwę** udziału możesz pozostawić wartość domyślną określoną przez podane dane lub określić nową nazwę odebranego udziału. 

   Po zaakceptowaniu warunków użytkowania i po wybraniu konta usługi Data Share do zarządzania odebranym udziałem wybierz pozycję **Zaakceptuj i skonfiguruj**. Zostanie utworzona subskrypcja udziału. 

   ![Zaakceptuj opcje](./media/accept-options.png "Zaakceptuj opcje") 

   Ta akcja przenosi Cię do odebranego udziału na twoim Data Share konta. 

   Jeśli nie chcesz akceptować zaproszenia, wybierz pozycję *Odrzuć*. 

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [az datashare consumer share-subscription create,](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_create) aby utworzyć Data Share.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Konfigurowanie odebranego udziału

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj poniższe kroki, aby skonfigurować miejsce, w którym chcesz odbierać dane.

1. Wybierz **kartę Zestawy** danych. Zaznacz pole wyboru obok zestawu danych, do którego chcesz przypisać miejsce docelowe. Wybierz **pozycję + Mapuj, aby** wybrać docelowy magazyn danych. 

   ![Mapowanie na element docelowy](./media/dataset-map-target.png "Mapowanie na element docelowy") 

1. Wybierz typ docelowego magazynu danych, do których mają trafiać dane. Wszystkie pliki danych lub tabele w docelowym magazynie danych o tej samej ścieżce i nazwie zostaną zastąpione. Jeśli odbierasz dane do systemu Azure SQL Database lub Azure Synapse Analytics (dawniej Azure SQL DW), zaznacz pole wyboru Zezwalaj Data Share na uruchamianie powyższego skryptu "utwórz użytkownika" w **moim imieniu.**

   W przypadku udostępniania w miejscu wybierz magazyn danych w określonej lokalizacji. Lokalizacja to centrum danych platformy Azure, w którym znajduje się źródłowy magazyn danych dostawcy danych. Gdy zestaw danych zostanie zamapowany, możesz użyć linku w ścieżce docelowej, aby uzyskać dostęp do danych.

   ![Docelowe konto magazynu](./media/dataset-map-target-sql.png "Magazyn docelowy") 

1. W przypadku udostępniania opartego na migawkach, jeśli dostawca danych utworzył harmonogram migawek w celu zapewnienia regularnej aktualizacji danych, możesz również włączyć harmonogram migawek, wybierając kartę **Harmonogram migawek.** Zaznacz pole wyboru obok harmonogramu migawek i wybierz **pozycję + Włącz**. Należy pamiętać, że pierwsza zaplanowana migawka rozpocznie się w ciągu jednej minuty od harmonogramu, a kolejne migawki będą rozpoczynać się w ciągu sekund od zaplanowanego czasu.

   ![Włączanie harmonogramu migawek](./media/enable-snapshot-schedule.png "Włączanie harmonogramu migawek")

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj tych poleceń, aby skonfigurować miejsce, w którym chcesz odbierać dane.

1. Uruchom polecenie [az datashare consumer share-subscription list-source-dataset,](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_dataset) aby uzyskać identyfikator zestawu danych:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Uruchom polecenie [az storage account create,](/cli/azure/storage/account#az_storage_account_create) aby utworzyć konto magazynu dla tego Data Share:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Użyj polecenia [az storage account show,](/cli/azure/storage/account#az_storage_account_show) aby uzyskać identyfikator konta magazynu:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Użyj następującego polecenia, aby uzyskać identyfikator jednostki konta:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Użyj polecenia [az role assignment create,](/cli/azure/role/assignment#az_role_assignment_create) aby utworzyć przypisanie roli dla jednostki konta:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Utwórz zmienną dla mapowania na podstawie identyfikatora zestawu danych:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Użyj polecenia [az datashare consumer dataset-mapping create,](/cli/azure/datashare/consumer/dataset-mapping#az_datashare_consumer_dataset_mapping_create) aby utworzyć mapowanie zestawu danych:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Uruchom polecenie [az datashare consumer share-subscription synchronization start,](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_start) aby rozpocząć synchronizację zestawu danych.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Uruchom polecenie [az datashare consumer share-subscription synchronization list,](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_list) aby wyświetlić listę synchronizacji:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Użyj polecenia [az datashare consumer share-subscription list-source-share-synchronization-setting,](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) aby wyświetlić ustawienia synchronizacji ustawione dla udziału.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Wyzwalanie migawki

### <a name="portal"></a>[Portal](#tab/azure-portal)

Te kroki dotyczą tylko udostępniania opartego na migawkach.

1. Migawkę można wyzwolić, wybierając **kartę Szczegóły,** a następnie **pozycję Wyzwolij migawkę.** W tym miejscu możesz wyzwolić pełną lub przyrostową migawkę danych. Jeśli po raz pierwszy otrzymujesz dane od dostawcy danych, wybierz pozycję Pełna kopia. 

   ![Migawka wyzwalacza](./media/trigger-snapshot.png "Migawka wyzwalacza") 

1. Po pomyślnym ostatnim uruchomieniu *przejdź* do docelowego magazynu danych, aby wyświetlić odebrane dane. Wybierz **pozycję Zestawy** danych i kliknij link w ścieżce docelowej. 

   ![Zestawy danych konsumentów](./media/consumer-datasets.png "Mapowanie zestawu danych odbiorców") 

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Uruchom polecenie [az datashare consumer trigger create,](/cli/azure/datashare/consumer/trigger#az_datashare_consumer_trigger_create) aby wyzwolić migawkę:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Tego polecenia należy używać tylko w przypadku udostępniania opartego na migawkach.

---

## <a name="view-history"></a>Wyświetlanie historii
Ten krok dotyczy tylko udostępniania opartego na migawkach. Aby wyświetlić historię migawek, wybierz **kartę** Historia. Tutaj znajdziesz historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 30 dni.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasób nie będzie już potrzebny, przejdź do Data Share **Przegląd** i wybierz pozycję **Usuń,** aby go usunąć.

## <a name="next-steps"></a>Następne kroki
W tym samouczku opisano sposób akceptowania i odbierania Azure Data Share. Aby dowiedzieć się więcej na Azure Data Share pojęć, przejdź do Azure Data Share Terminologia.

> [!div class="nextstepaction"]
> [Azure Data Share pojęcia](terminology.md)
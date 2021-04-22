---
title: 'Samouczek: udostępnianie poza twoją Azure Data Share'
description: Samouczek — udostępnianie danych klientom i partnerom przy użyciu Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: b8d49e3b3c6f6407fe241e00ada5039bd94fd706
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870881"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Samouczek: Udostępnianie danych przy użyciu usługi Azure Data Share  

Z tego samouczka dowiesz się, jak skonfigurować nową usługę Azure Data Share rozpocząć udostępnianie danych klientom i partnerom spoza organizacji platformy Azure. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz usługę Data Share.
> * Dodaj zestawy danych do usługi Data Share.
> * Włącz harmonogram migawek dla Data Share. 
> * Dodaj odbiorców do usługi Data Share. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: jeśli nie masz subskrypcji platformy Azure, przed [rozpoczęciem](https://azure.microsoft.com/free/) utwórz bezpłatne konto.
* Adres e-mail logowania do platformy Azure adresata (użycie aliasu adresu e-mail nie będzie działać).
* Jeśli źródłowy magazyn danych platformy Azure znajduje się w innej subskrypcji platformy Azure niż ta, której użyjesz do utworzenia zasobu usługi Data Share, zarejestruj dostawcę zasobów [Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji, w której znajduje się magazyn danych platformy Azure. 

### <a name="share-from-a-storage-account"></a>Udostępnianie z konta magazynu

* Konto usługi Azure Storage: jeśli jeszcze go nie masz, możesz utworzyć konto [usługi Azure Storage](../storage/common/storage-account-create.md)
* Uprawnienie do zapisu na koncie magazynu, które znajduje się w *microsoft.storage/storageAccounts/write.* To uprawnienie istnieje w roli **Współautor**.
* Uprawnienie do dodawania przypisania roli do konta magazynu, które znajduje się w *microsoft.authorization/role assignments/write.* To uprawnienie istnieje w roli **Właściciel**. 


### <a name="share-from-a-sql-based-source"></a>Udostępnianie ze źródła opartego na języku SQL
Poniżej znajduje się lista wymagań wstępnych dotyczących udostępniania danych ze źródła SQL. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Wymagania wstępne dotyczące udostępniania z Azure SQL Database lub Azure Synapse Analytics (wcześniej Azure SQL DW)

* Konto Azure SQL Database lub Azure Synapse Analytics (dawniej Azure SQL DW) z tabelami i widokami, które chcesz udostępnić.
* Uprawnienie do zapisu w bazach danych na serwerze SQL, które znajduje się w *Microsoft.Sql/servers/databases/write.* To uprawnienie istnieje w roli **Współautor**.
* **Azure Active Directory administratora** serwera SQL
* SQL Server dostępu zapory. W tym celu należy wykonać następujące czynności: 
    1. W Azure Portal przejdź do serwera SQL. Wybierz *pozycję Zapory i sieci wirtualne w lewym* panelu nawigacyjnym.
    1. Kliknij **przycisk Tak,** *aby zezwolić usługom i zasobom platformy Azure na dostęp do tego serwera.*
    1. Kliknij **pozycję +Dodaj adres IP klienta.** Adres IP klienta może ulec zmianie. Może być konieczne powtórzenie tego procesu przy następnym udostępnianiu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Wymagania wstępne dotyczące udostępniania z puli SQL Azure Synapse Analytics (obszaru roboczego)

* * Dedykowana pula SQL Azure Synapse Analytics (obszar roboczy) z tabelami, które chcesz udostępnić. Udostępnianie widoku nie jest obecnie obsługiwane. Udostępnianie z bez serwera puli SQL nie jest obecnie obsługiwane.
* Uprawnienie do zapisu w puli SQL w obszarze roboczym usługi Synapse, który znajduje się w *microsoft.synapse/workspaces/sqlPools/write.* To uprawnienie istnieje w roli **Współautor**.
* Uprawnienie dla tożsamości Data Share zarządzanego zasobu do uzyskiwania dostępu do puli SQL obszaru roboczego usługi Synapse. Można to zrobić za pomocą następujących kroków: 
    1. W Azure Portal przejdź do obszaru roboczego synapse. Wybierz pozycję Administrator usługi SQL Active Directory w lewym menu nawigacyjnym i ustaw siebie jako **Azure Active Directory administratora.**
    1. Otwórz Synapse Studio wybierz pozycję *Zarządzaj w* lewym panelu nawigacyjnym. Wybierz *pozycję Kontrola dostępu w* obszarze Zabezpieczenia. Przypisz sobie **rolę administratora SQL** lub administratora obszaru **roboczego.**
    1. W Synapse Studio wybierz pozycję *Opracuj* w lewym panelu nawigacyjnym. Wykonaj następujący skrypt w puli SQL, aby dodać Data Share tożsamości zarządzanej zasobu jako db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Pamiętaj, *<share_acc_name>* to nazwa zasobu Data Share zasobów. Jeśli jeszcze nie utworzono zasobu Data Share, możesz wrócić do tego wymagania wstępnego później.  

* Dostęp zapory obszaru roboczego usługi Synapse. Można to zrobić za pomocą następujących kroków: 
    1. W Azure Portal przejdź do obszaru roboczego synapse. Wybierz *pozycję Zapory w lewym* panelu nawigacyjnym.
    1. Kliknij **pozycję WŁ.,** *aby zezwolić usługom i zasobom platformy Azure na dostęp do tego obszaru roboczego.*
    1. Kliknij **pozycję +Dodaj adres IP klienta.** Adres IP klienta może ulec zmianie. Może być konieczne powtórzenie tego procesu przy następnym udostępnianiu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 


### <a name="share-from-azure-data-explorer"></a>Udostępnianie z usługi Azure Data Explorer
* Klaster Azure Data Explorer z bazami danych, które chcesz udostępnić.
* Uprawnienie do zapisu w klastrze Azure Data Explorer, który znajduje się w *microsoft.kusto/clusters/write.* To uprawnienie istnieje w roli **Współautor**.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Tworzenie konta Data Share kont

### <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz zasób Azure Data Share w grupie zasobów platformy Azure.

1. Wybierz przycisk menu w lewym górnym rogu portalu, a następnie wybierz pozycję **Utwórz zasób** (+).

1. Wyszukaj *Data Share*.

1. Wybierz Data Share wybierz pozycję **Utwórz.**

1. Wypełnij podstawowe szczegóły zasobu Azure Data Share, aby uzyskać następujące informacje. 

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć dla konta udziału danych.|
    | Grupa zasobów | *testresourcegroup* | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Lokalizacja | *Wschodnie stany USA 2* | Wybierz region dla konta udziału danych.
    | Nazwa | *datashareaccount* | Określ nazwę konta udziału danych. |
    | | |

1. Wybierz **pozycję Przejrzyj i utwórz,** a następnie pozycję **Utwórz,** aby aprowizować konto udziału danych. Aprowizowanie nowego konta udziału danych zwykle trwa około 2 minut lub mniej. 

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Utwórz zasób Azure Data Share w grupie zasobów platformy Azure.

Rozpocznij od przygotowania środowiska dla interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Użyj tych poleceń, aby utworzyć zasób:

1. Użyj polecenia [az account set,](/cli/azure/account#az_account_set) aby ustawić subskrypcję jako bieżącą subskrypcję domyślną:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Uruchom polecenie [az provider register,](/cli/azure/provider#az_provider_register) aby zarejestrować dostawcę zasobów:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Uruchom polecenie [az group create](/cli/azure/group#az_group_create), aby utworzyć grupę zasobów lub użyć istniejącej grupy zasobów:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Uruchom polecenie [az datashare account create,](/cli/azure/datashare/account#az_datashare_account_create) aby utworzyć Data Share konto:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Uruchom polecenie [az datashare account list,](/cli/azure/datashare/account#az_datashare_account_list) aby wyświetlić Data Share kont:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Tworzenie udziału

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do strony Data Share Przegląd.

    ![Udostępnianie danych](./media/share-receive-data.png "Udostępnianie danych") 

1. Wybierz **pozycję Rozpocznij udostępnianie danych.**

1. Wybierz przycisk **Utwórz**.   

1. Wypełnij szczegóły udziału. Określ nazwę, typ udziału, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![EnterShareDetails](./media/enter-share-details.png "Wprowadzanie szczegółów udziału") 

1. Wybierz opcję **Kontynuuj**.

1. Aby dodać zestawy danych do udziału, wybierz **pozycję Dodaj zestawy danych.** 

    ![Dodawanie zestawów danych do udziału](./media/datasets.png "Zestawy danych")

1. Wybierz typ zestawu danych, który chcesz dodać. Zostanie wyświetlona inna lista typów zestawów danych w zależności od typu udziału (migawka lub w miejscu) wybranego w poprzednim kroku. Jeśli udostępnianie z usługi Azure SQL Database lub Azure Synapse Analytics (dawniej Azure SQL DW), zostanie wyświetlony monit o metodę uwierzytelniania w celu udostępnienia listy tabel. Wybierz pozycję Uwierzytelnianie usługi AAD i zaznacz pole wyboru Data Share uruchamianie powyższego skryptu **"utwórz użytkownika" w moim imieniu.** 

    ![AddDatasets](./media/add-datasets.png "Dodawanie zestawów danych")    

1. Przejdź do obiektu, który chcesz udostępnić, i wybierz pozycję "Dodaj zestawy danych". 

    ![SelectDatasets (Zestawy danych)](./media/select-datasets.png "Wybieranie zestawów danych")    

1. Na karcie Adresaci wprowadź adresy e-mail użytkownika danych, wybierając pozycję "+ Dodaj adresata". 

    ![AddRecipients](./media/add-recipient.png "Dodawanie adresatów") 

1. Wybierz opcję **Kontynuuj**.

1. Jeśli wybrano typ udziału migawki, można skonfigurować harmonogram migawek w celu udostępniania aktualizacji danych odbiorców danych. 

    ![EnableSnapshots](./media/enable-snapshots.png "Włączanie migawek") 

1. Wybierz czas rozpoczęcia i interwał cyklu. 

1. Wybierz opcję **Kontynuuj**.

1. Na karcie Przeglądanie + tworzenie przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Wybierz przycisk **Utwórz**.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Uruchom polecenie [az storage account create,](/cli/azure/storage/account#az_storage_account_create) aby utworzyć Data Share:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Użyj polecenia [az storage container create,](/cli/azure/storage/container#az_storage_container_create) aby utworzyć kontener dla udziału w poprzednim poleceniu:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Uruchom polecenie [az datashare create,](/cli/azure/datashare#az_datashare_create) aby utworzyć Data Share:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Użyj polecenia [az datashare invitation create,](/cli/azure/datashare/invitation#az_datashare_invitation_create) aby utworzyć zaproszenie dla określonego adresu:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

Twoja Azure Data Share została utworzona, a adresat twojego Data Share jest teraz gotowy do zaakceptowania Zaproszenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasób nie będzie już potrzebny, przejdź do Data Share **Przegląd** i wybierz pozycję **Usuń,** aby go usunąć.

## <a name="next-steps"></a>Następne kroki

Z tego samouczka dowiesz się, jak utworzyć Azure Data Share i zaprosić adresatów. Aby dowiedzieć się, jak konsument danych może akceptować i odbierać udział danych, przejdź do samouczka akceptowania i odbierania danych.

> [!div class="nextstepaction"]
> [Samouczek: Akceptowanie i odbieranie danych przy użyciu usługi Azure Data Share](subscribe-to-data-share.md)

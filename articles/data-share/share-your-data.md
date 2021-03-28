---
title: 'Samouczek: udostępnianie poza swoją organizacji — udział danych platformy Azure'
description: Samouczek — udostępnianie danych klientom i partnerom za pomocą udziału danych platformy Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 8e149270d8f98cbf72d3864d238a3d8ddfd61c67
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639545"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Samouczek: Udostępnianie danych przy użyciu usługi Azure Data Share  

W tym samouczku dowiesz się, jak skonfigurować nowy udział danych platformy Azure i zacząć udostępniać dane klientom i partnerom spoza organizacji platformy Azure. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz usługę Data Share.
> * Dodaj zestawy danych do usługi Data Share.
> * Włącz harmonogram migawek dla udziału danych. 
> * Dodaj odbiorców do usługi Data Share. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Adres e-mail logowania odbiorcy platformy Azure (przy użyciu aliasu poczty e-mail nie będzie działał).
* Jeśli źródłowy magazyn danych platformy Azure znajduje się w innej subskrypcji platformy Azure niż ta, która będzie używana do tworzenia zasobu udziału danych, zarejestruj [dostawcę zasobów Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji, w której znajduje się magazyn danych platformy Azure. 

### <a name="share-from-a-storage-account"></a>Udostępnianie z konta magazynu

* Konto usługi Azure Storage: Jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](../storage/common/storage-account-create.md)
* Uprawnienie do zapisu na koncie magazynu, które jest obecne w usłudze *Microsoft. Storage/storageAccounts/Write*. To uprawnienie istnieje w roli **Współautor**.
* Uprawnienie do dodawania przypisania roli do konta magazynu, które jest obecne w *firmie Microsoft. Autoryzacja/przypisania ról/zapis*. To uprawnienie istnieje w roli **Właściciel**. 


### <a name="share-from-a-sql-based-source"></a>Udostępnianie z poziomu źródła opartego na języku SQL
Poniżej znajduje się lista wymagań wstępnych dotyczących udostępniania danych ze źródła SQL. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Wymagania wstępne dotyczące udostępniania z usług Azure SQL Database lub Azure Synapse Analytics (dawniej: Azure SQL DW)

* Azure SQL Database lub usługa Azure Synapse Analytics (dawniej usługa Azure SQL DW) z tabelami i widokami, które chcesz udostępnić.
* Uprawnienia do zapisu w bazach danych programu SQL Server, które znajdują się w *Microsoft. SQL/serwery/bazy danych/zapis*. To uprawnienie istnieje w roli **Współautor**.
* **Azure Active Directory administrator** programu SQL Server
* SQL Server dostęp do zapory. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do programu SQL Server. Wybierz *zapory i sieci wirtualne* z nawigacji po lewej stronie.
    1. Kliknij  przycisk tak *, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego serwera*.
    1. Kliknij pozycję **+ Dodaj adres IP klienta**. Adres IP klienta może ulec zmianie. Ten proces może wymagać powtarzania przy następnym udostępnieniu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Wymagania wstępne dotyczące udostępniania z puli SQL usługi Azure Synapse Analytics (Workspace)

* * Dedykowana Pula SQL usługi Azure Synapse Analytics z tabelami, które chcesz udostępnić. Udostępnianie widoku nie jest obecnie obsługiwane. Udostępnianie z puli SQL bez serwera nie jest obecnie obsługiwane.
* Uprawnienia do zapisu w puli SQL w obszarze roboczym Synapse, które znajdują się w *witrynie Microsoft. Synapse/Workspaces/Sqlpools/Write*. To uprawnienie istnieje w roli **Współautor**.
* Uprawnienie do zarządzanej tożsamości zasobu udziału danych w celu uzyskania dostępu do puli SQL obszaru roboczego Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do obszaru roboczego Synapse. Wybierz pozycję SQL Active Directory administrator na lewym panelu nawigacyjnym i ustaw siebie jako **administratora Azure Active Directory**.
    1. Otwórz program Synapse Studio, wybierz pozycję *Zarządzaj* w lewym obszarze nawigacji. Wybierz pozycję *Kontrola dostępu* w obszarze zabezpieczenia. Przypisz siebie do roli administratora **SQL** lub **obszaru roboczego** .
    1. W programie Synapse Studio wybierz pozycję *programowanie* na lewym pasku nawigacyjnym. Wykonaj następujący skrypt w puli SQL, aby dodać tożsamość zarządzaną zasobu udziału danych jako db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Należy pamiętać, że *<share_acc_name>* to nazwa zasobu udziału danych. Jeśli zasób udział danych nie został jeszcze utworzony, możesz wrócić do tego wymagania wstępnego później.  

* Dostęp do zapory obszaru roboczego Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do obszaru roboczego Synapse. Wybierz pozycję *zapory* na lewym pasku nawigacyjnym.
    1. Kliknij  pozycję Włącz *, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego obszaru roboczego*.
    1. Kliknij pozycję **+ Dodaj adres IP klienta**. Adres IP klienta może ulec zmianie. Ten proces może wymagać powtarzania przy następnym udostępnieniu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 


### <a name="share-from-azure-data-explorer"></a>Udostępnianie z usługi Azure Data Explorer
* Klaster Eksplorator danych platformy Azure z bazami danych, które chcesz udostępnić.
* Uprawnienie do zapisu w klastrze Eksplorator danych platformy Azure, który znajduje się w *Microsoft. Kusto/klastrów/Write*. To uprawnienie istnieje w roli **Współautor**.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Tworzenie konta udziału danych

### <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz zasób udziału danych platformy Azure w grupie zasobów platformy Azure.

1. Wybierz przycisk menu w lewym górnym rogu portalu, a następnie wybierz pozycję **Utwórz zasób** (+).

1. Wyszukaj *udział danych*.

1. Wybierz pozycję udział danych i wybierz pozycję **Utwórz**.

1. Wypełnij podstawowe szczegóły zasobu udziału danych platformy Azure, korzystając z poniższych informacji. 

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla konta udziału danych.|
    | Grupa zasobów | *testresourcegroup* | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Lokalizacja | *Wschodnie stany USA 2* | Wybierz region dla konta udziału danych.
    | Nazwa | *datashareaccount* | Określ nazwę konta udziału danych. |
    | | |

1. Wybierz pozycję **Przegląd + Utwórz**, a następnie pozycję **Utwórz** , aby zainicjować obsługę konta udziału danych. Inicjowanie obsługi nowego konta udziału danych zwykle trwa około 2 minuty. 

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Utwórz zasób udziału danych platformy Azure w grupie zasobów platformy Azure.

Rozpocznij od przygotowania środowiska dla interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Użyj następujących poleceń, aby utworzyć zasób:

1. Użyj polecenia [AZ Account Set](/cli/azure/account#az_account_set) , aby ustawić subskrypcję jako bieżącą subskrypcję domyślną:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Uruchom polecenie [AZ Provider Register](/cli/azure/provider#az_provider_register) , aby zarejestrować dostawcę zasobów:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Uruchom polecenie [az group create](/cli/azure/group#az_group_create), aby utworzyć grupę zasobów lub użyć istniejącej grupy zasobów:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Uruchom polecenie [AZ datashare Account Create](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_create) , aby utworzyć konto udziału danych:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Uruchom polecenie [AZ datashare Account List](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_list) , aby wyświetlić konta udziałów danych:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Tworzenie udziału

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do strony Przegląd udostępniania danych.

    ![Udostępnianie danych](./media/share-receive-data.png "Udostępnianie danych") 

1. Wybierz pozycję **Rozpocznij udostępnianie danych**.

1. Wybierz przycisk **Utwórz**.   

1. Wprowadź szczegółowe informacje o udziale. Określ nazwę, typ udziału, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![EnterShareDetails](./media/enter-share-details.png "Wprowadź szczegóły udostępniania") 

1. Wybierz opcję **Kontynuuj**.

1. Aby dodać zestawy danych do udziału, wybierz pozycję **Dodaj zestawy danych**. 

    ![Dodawanie zestawów danych do udziału](./media/datasets.png "Zestawy danych")

1. Wybierz typ zestawu danych, który chcesz dodać. Zostanie wyświetlona inna lista typów zestawów danych w zależności od typu udziału (migawka lub miejsce w miejscu) wybranej w poprzednim kroku. W przypadku udostępniania z Azure SQL Database lub usługi Azure Synapse Analytics (dawniej Azure SQL DW) zostanie wyświetlony monit o metodę uwierzytelniania, aby wyświetlić listę tabel. Wybierz pozycję Uwierzytelnianie w usłudze AAD, a następnie zaznacz pole wyboru **Zezwalaj usłudze udostępnianie danych na uruchamianie skryptu "Create User" w moim imieniu**. 

    ![Adddatasets](./media/add-datasets.png "Dodaj zestawy danych")    

1. Przejdź do obiektu, który chcesz udostępnić, i wybierz pozycję "Dodaj zestawy danych". 

    ![SelectDatasets](./media/select-datasets.png "Wybierz zestawy danych")    

1. Na karcie adresaci wprowadź adres e-mail odbiorcy danych, wybierając pozycję "+ Dodaj odbiorcę". 

    ![Addrecipients](./media/add-recipient.png "Dodawanie adresatów") 

1. Wybierz opcję **Kontynuuj**.

1. W przypadku wybrania typu udziału migawek można skonfigurować harmonogram migawek w taki sposób, aby dostarczał aktualizacje danych do konsumenta danych. 

    ![EnableSnapshots](./media/enable-snapshots.png "Włącz migawki") 

1. Wybierz czas rozpoczęcia i interwał cyklu. 

1. Wybierz opcję **Kontynuuj**.

1. Na karcie Recenzja + tworzenie przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Wybierz przycisk **Utwórz**.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Uruchom polecenie [AZ Storage account Create](/cli/azure/storage/account#az_storage_account_create) , aby utworzyć udział danych:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Użyj polecenia [AZ Storage Container Create](/cli/azure/storage/container#az_storage_container_create) , aby utworzyć kontener dla udziału w poprzednim poleceniu:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Uruchom polecenie [AZ datashare Create](/cli/azure/ext/datashare/datashare#ext_datashare_az_datashare_create) , aby utworzyć udział danych:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Użyj polecenia [AZ datashare zaproszenia Create](/cli/azure/ext/datashare/datashare/invitation#ext_datashare_az_datashare_invitation_create) , aby utworzyć zaproszenie dla określonego adresu:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

Udział danych platformy Azure został utworzony, a odbiorca Twojego udziału danych jest teraz gotowy do zaakceptowania Twojego zaproszenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasób nie jest już wymagany, przejdź do strony **Przegląd udziału danych** i wybierz pozycję **Usuń** , aby go usunąć.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia udziału danych platformy Azure i zapraszania adresatów. Aby dowiedzieć się, jak odbiorca danych może zaakceptować i odebrać udział danych, przejdź do samouczka akceptowanie i odbieranie danych.

> [!div class="nextstepaction"]
> [Samouczek: Akceptowanie i odbieranie danych przy użyciu usługi Azure Data Share](subscribe-to-data-share.md)

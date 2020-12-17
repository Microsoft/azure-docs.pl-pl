---
title: Udostępnianie i odbieranie danych z usług Azure Blob Storage i Azure Data Lake Storage
description: Dowiedz się, jak udostępniać i odbierać dane z usługi Azure Blob Storage i Azure Data Lake Storage
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 9dfc8be54fc55842440e376916b2eb9bb04a4610
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617089"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Udostępnianie i odbieranie danych z usług Azure Blob Storage i Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Udział danych platformy Azure obsługuje udostępnianie oparte na migawce z konta magazynu. W tym artykule wyjaśniono, jak udostępniać i odbierać dane z następujących źródeł: Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2.

Udział danych platformy Azure obsługuje udostępnianie plików, folderów i systemów plików z Azure Data Lake Gen1 i Azure Data Lake Gen2. Obsługuje także udostępnianie obiektów blob, folderów i kontenerów z usługi Azure Blob Storage. Obecnie jest obsługiwany tylko blokowy obiekt BLOB. Dane udostępnione z tych źródeł można odbierać do Azure Data Lake Gen2 lub Blob Storage platformy Azure.

Gdy systemy plików, kontenery lub foldery są udostępniane w ramach udostępniania opartego na migawce, konsument danych może wybrać pełną kopię danych udziału lub użyć funkcji przyrostowej migawki w celu skopiowania tylko nowych lub zaktualizowanych plików. Przyrostowa migawka jest określana na podstawie czasu ostatniej modyfikacji plików. Istniejące pliki o tej samej nazwie zostaną zastąpione podczas tworzenia migawek. Plik usunięty ze źródła nie został usunięty z elementu docelowego. Puste podfoldery w źródle nie są kopiowane do obiektu docelowego. 

## <a name="share-data"></a>Udostępnianie danych

### <a name="prerequisites-to-share-data"></a>Wymagania wstępne dotyczące udostępniania danych

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Adres e-mail logowania odbiorcy platformy Azure (przy użyciu aliasu poczty e-mail nie będzie działał).
* Jeśli źródłowy magazyn danych platformy Azure znajduje się w innej subskrypcji platformy Azure niż ta, która będzie używana do tworzenia zasobu udziału danych, zarejestruj [dostawcę zasobów Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji, w której znajduje się magazyn danych platformy Azure. 

### <a name="prerequisites-for-source-storage-account"></a>Wymagania wstępne dotyczące źródłowego konta magazynu

* Konto usługi Azure Storage: Jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](../storage/common/storage-account-create.md)
* Uprawnienie do zapisu na koncie magazynu, które jest obecne w usłudze *Microsoft. Storage/storageAccounts/Write*. To uprawnienie istnieje w roli Współautor.
* Uprawnienie do dodawania przypisania roli do konta magazynu, które jest obecne w *firmie Microsoft. Autoryzacja/przypisania ról/zapis*. To uprawnienie istnieje w roli Właściciel. 

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Tworzenie konta udziału danych

Utwórz zasób udziału danych platformy Azure w grupie zasobów platformy Azure.

1. Wybierz przycisk menu w lewym górnym rogu portalu, a następnie wybierz pozycję **Utwórz zasób** (+).

1. Wyszukaj *udział danych*.

1. Wybierz pozycję udział danych i wybierz pozycję **Utwórz**.

1. Wypełnij podstawowe szczegóły zasobu udziału danych platformy Azure, korzystając z poniższych informacji. 

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla konta udziału danych.|
    | Grupa zasobów | *Testuj grupę zasobów* | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Lokalizacja | *Wschodnie stany USA 2* | Wybierz region dla konta udziału danych.
    | Nazwa | *datashareaccount* | Określ nazwę konta udziału danych. |
    | | |

1. Wybierz pozycję **Przegląd + Utwórz**, a następnie pozycję **Utwórz** , aby zainicjować obsługę konta udziału danych. Inicjowanie obsługi nowego konta udziału danych zwykle trwa około 2 minuty. 

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

### <a name="create-a-share"></a>Tworzenie udziału

1. Przejdź do strony Przegląd udostępniania danych.

    ![Udostępnianie danych](./media/share-receive-data.png "Udostępnianie danych") 

1. Wybierz pozycję **Rozpocznij udostępnianie danych**.

1. Wybierz pozycję **Utwórz**.   

1. Wprowadź szczegółowe informacje o udziale. Określ nazwę, typ udziału, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![EnterShareDetails](./media/enter-share-details.png "Wprowadź szczegóły udostępniania") 

1. Wybierz opcję **Kontynuuj**.

1. Aby dodać zestawy danych do udziału, wybierz pozycję **Dodaj zestawy danych**. 

    ![Dodawanie zestawów danych do udziału](./media/datasets.png "Zestawy danych")

1. Wybierz typ zestawu danych, który chcesz dodać. Zostanie wyświetlona inna lista typów zestawów danych w zależności od typu udziału (migawka lub miejsce w miejscu) wybranej w poprzednim kroku. 

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

1. Na karcie Recenzja + tworzenie przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Wybierz pozycję **Utwórz**.

Udział danych platformy Azure został utworzony, a odbiorca Twojego udziału danych jest teraz gotowy do zaakceptowania Twojego zaproszenia. 

## <a name="receive-data"></a>Odbierz dane

### <a name="prerequisites-to-receive-data"></a>Wymagania wstępne dotyczące odbierania danych
Przed zaakceptowaniem zaproszenia do udziału danych musisz udostępnić kilka zasobów platformy Azure, które są wymienione poniżej. 

Przed zaakceptowaniem zaproszenia udziału danych upewnij się, że wszystkie wymagania wstępne zostały ukończone. 

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Zaproszenie do udziału danych: zaproszenie od Microsoft Azure z podmiotem zatytułowanym "zaproszenie do udziału danych platformy Azure **<yourdataprovider@domain.com>** ".
* Zarejestruj [dostawcę zasobów Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji platformy Azure, w której utworzysz zasób udziału danych i subskrypcję platformy Azure, w której znajdują się docelowe magazyny danych platformy Azure.

### <a name="prerequisites-for-target-storage-account"></a>Wymagania wstępne dotyczące docelowego konta magazynu

* Konto usługi Azure Storage: Jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](../storage/common/storage-account-create.md). 
* Uprawnienie do zapisu na koncie magazynu, które jest obecne w usłudze *Microsoft. Storage/storageAccounts/Write*. To uprawnienie istnieje w roli Współautor. 
* Uprawnienie do dodawania przypisania roli do konta magazynu, które jest obecne w *firmie Microsoft. Autoryzacja/przypisania ról/zapis*. To uprawnienie istnieje w roli Właściciel.  

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

### <a name="open-invitation"></a>Otwórz zaproszenie

1. Możesz otworzyć zaproszenie z poczty e-mail lub bezpośrednio z Azure Portal. 

   Aby otworzyć zaproszenie z poczty e-mail, sprawdź skrzynkę odbiorczą dla zaproszenia od dostawcy danych. Zaproszenie pochodzi z Microsoft Azure, zatytułowane **zaproszenie <yourdataprovider@domain.com> udziału danych platformy Azure**. Kliknij pozycję **Wyświetl zaproszenie** , aby zobaczyć zaproszenie na platformie Azure. 

   Aby otworzyć zaproszenie z Azure Portal bezpośrednio, Wyszukaj **zaproszenia udziału danych** w Azure Portal. Spowoduje to przejście do listy zaproszeń udziału danych.

   ![Lista zaproszeń](./media/invitations.png "Lista zaproszeń") 

1. Wybierz udział, który chcesz wyświetlić. 

### <a name="accept-invitation"></a>Zaakceptuj zaproszenie
1. Upewnij się, że wszystkie pola są przeglądane, w tym **warunki użytkowania**. Jeśli akceptujesz warunki użytkowania, musisz zaznaczyć pole wyboru, aby wskazać, że zgadzasz się. 

   ![Warunki użytkowania](./media/terms-of-use.png "Warunki użytkowania") 

1. W obszarze *docelowe konto udziału danych* wybierz subskrypcję i grupę zasobów, w ramach której będziesz wdrażać udział danych. 

   W polu **konto udostępniania danych** wybierz pozycję **Utwórz nową** , jeśli nie masz istniejącego konta udziału danych. W przeciwnym razie wybierz istniejące konto udziału danych, do którego chcesz zaakceptować udział danych. 

   W polu **Nazwa otrzymanego udziału** można pozostawić wartość domyślną określoną przez dane lub określić nową nazwę dla odebranego udziału. 

   Po uzgodnieniu warunków użytkowania i określeniu konta udziału danych do zarządzania odebranym udziałem wybierz pozycję **Zaakceptuj i skonfiguruj**. Zostanie utworzona subskrypcja udziału. 

   ![Zaakceptuj opcje](./media/accept-options.png "Zaakceptuj opcje") 

   Spowoduje to przejście do otrzymanego udziału w Twoim koncie udostępniania danych. 

   Jeśli nie chcesz zaakceptować zaproszenia, wybierz pozycję *Odrzuć*. 

### <a name="configure-received-share"></a>Konfiguruj odebrany udział
Wykonaj poniższe kroki, aby skonfigurować miejsce, w którym chcesz otrzymywać dane.

1. Wybierz kartę **zestawy danych** . Zaznacz pole wyboru obok zestawu danych, do którego chcesz przypisać miejsce docelowe. Wybierz pozycję **+ Mapuj, aby** wybrać docelowy magazyn danych. 

   ![Mapuj do elementu docelowego](./media/dataset-map-target.png "Mapuj do elementu docelowego") 

1. Wybierz docelowy magazyn danych, w którym chcesz umieścić dane. Wszystkie pliki danych w docelowym magazynie danych o tej samej ścieżce i nazwie zostaną zastąpione. 

   ![Docelowe konto magazynu](./media/map-target.png "Magazyn docelowy") 

1. W przypadku udostępniania opartego na migawce, jeśli dostawca danych utworzył harmonogram migawek w celu zapewnienia regularnej aktualizacji danych, można również włączyć harmonogram migawek, wybierając kartę **harmonogram migawek** . Zaznacz pole wyboru obok harmonogramu migawek i wybierz pozycję **+ Włącz**.

   ![Włącz harmonogram migawek](./media/enable-snapshot-schedule.png "Włącz harmonogram migawek")

### <a name="trigger-a-snapshot"></a>Wyzwalanie migawki
Te kroki dotyczą tylko udostępniania opartego na migawce.

1. Możesz wyzwolić migawkę, wybierając kartę **szczegóły** , a następnie pozycję **Wyzwalaj migawkę**. Tutaj można wyzwolić pełną lub przyrostową migawkę danych. Jeśli po raz pierwszy otrzymujesz dane od dostawcy danych, wybierz pozycję pełna kopia. 

   ![Wyzwalanie migawki](./media/trigger-snapshot.png "Wyzwalanie migawki") 

1. Po *pomyślnym* zakończeniu ostatniego uruchomienia Przejdź do docelowego magazynu danych, aby wyświetlić odebrane dane. Wybierz pozycję **zestawy danych**, a następnie kliknij link w ścieżce docelowej. 

   ![Zestawy danych dla odbiorców](./media/consumer-datasets.png "Mapowanie zestawu danych klienta") 

### <a name="view-history"></a>Wyświetlanie historii
Ten krok dotyczy tylko udostępniania opartego na migawce. Aby wyświetlić historię migawek, wybierz pozycję Karta **historia** . W tym miejscu znajdziesz historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 30 dni. 

## <a name="next-steps"></a>Następne kroki
Wiesz już, jak udostępniać i odbierać dane z konta magazynu przy użyciu usługi Azure Data Share Service. Aby dowiedzieć się więcej o udostępnianiu z innych źródeł danych, przejdź do [obsługiwanych magazynów danych](supported-data-stores.md).
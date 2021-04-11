---
title: Udostępnianie i odbieranie danych z usług Azure Blob Storage i Azure Data Lake Storage
description: Dowiedz się, jak udostępniać i odbierać dane z usługi Azure Blob Storage i Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/23/2021
ms.openlocfilehash: c94d6f7d4d06d7dbaa6a2ad54f94575a6cc85d33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644653"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Udostępnianie i odbieranie danych z usług Azure Blob Storage i Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Udział danych platformy Azure obsługuje udostępnianie oparte na migawce z konta magazynu. W tym artykule wyjaśniono, jak udostępniać i odbierać dane z usługi Azure Blob Storage, Azure Data Lake Storage Gen1 i Azure Data Lake Storage Gen2.

Udział danych platformy Azure obsługuje udostępnianie plików, folderów i systemów plików z Azure Data Lake Gen1 i Azure Data Lake Gen2. Obsługuje także udostępnianie obiektów blob, folderów i kontenerów z usługi Azure Blob Storage. Obecnie obsługiwane są tylko blokowe obiekty blob. Dane udostępniane z tych źródeł mogą być odbierane przez Azure Data Lake Gen2 lub Blob Storage platformy Azure.

Gdy systemy plików, kontenery i foldery są udostępniane w ramach udostępniania opartego na migawce, użytkownicy danych mogą wybrać pełną kopię danych udziału. Można też użyć funkcji migawki przyrostowej, aby skopiować tylko nowe lub zaktualizowane pliki. Możliwość tworzenia migawek przyrostowych zależy od czasu ostatniej modyfikacji plików. 

Istniejące pliki, które mają taką samą nazwę, są zastępowane podczas tworzenia migawki. Plik usunięty ze źródła nie został usunięty z obiektu docelowego. Puste podfoldery w źródle nie są kopiowane do obiektu docelowego. 

## <a name="share-data"></a>Udostępnianie danych

Skorzystaj z informacji w poniższych sekcjach, aby udostępnić dane za pomocą udziału danych platformy Azure. 
### <a name="prerequisites-to-share-data"></a>Wymagania wstępne dotyczące udostępniania danych

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Znajdź adres e-mail logowania odbiorcy platformy Azure. Alias adresu e-mail adresata nie będzie działał w Twoich celach.
* Jeśli źródłowy magazyn danych platformy Azure znajduje się w innej subskrypcji platformy Azure niż ta, w której utworzysz zasób udziału danych, zarejestruj [dostawcę zasobów Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji, w której znajduje się magazyn danych platformy Azure. 

### <a name="prerequisites-for-the-source-storage-account"></a>Wymagania wstępne dotyczące źródłowego konta magazynu

* Konto usługi Azure Storage. Jeśli nie masz jeszcze konta, [Utwórz je](../storage/common/storage-account-create.md).
* Uprawnienie do zapisu na koncie magazynu. Uprawnienie do zapisu w usłudze *Microsoft. Storage/storageAccounts/Write*. Jest częścią roli współautor.
* Uprawnienie do dodawania przypisania roli do konta magazynu. To uprawnienie jest w *firmie Microsoft. Autoryzacja/przypisania ról/zapis*. Jest częścią roli właściciela. 

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Tworzenie konta udziału danych

Utwórz zasób udziału danych platformy Azure w grupie zasobów platformy Azure.

1. W lewym górnym rogu portalu Otwórz menu, a następnie wybierz pozycję **Utwórz zasób** (+).

1. Wyszukaj *udział danych*.

1. Wybierz opcję **udział danych** i **Utwórz**.

1. Podaj podstawowe szczegóły zasobu udziału danych platformy Azure: 

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure dla konta udziału danych.|
    | Grupa zasobów | *Testuj grupę zasobów* | Użyj istniejącej grupy zasobów lub Utwórz grupę zasobów. |
    | Lokalizacja | *Wschodnie stany USA 2* | Wybierz region dla konta udziału danych.
    | Nazwa | *datashareaccount* | Nadaj nazwę swojemu kontu danych. |
    | | |

1. Wybierz pozycję **Przegląd + Utwórz**  >  **Utwórz** , aby zainicjować obsługę konta udziału danych. Inicjowanie obsługi nowego konta udziału danych trwa zwykle około 2 minuty. 

1. Po zakończeniu wdrożenia wybierz pozycję **Przejdź do zasobu**.

### <a name="create-a-share"></a>Tworzenie udziału

1. Przejdź do strony **Przegląd** udziału danych.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Zrzut ekranu przedstawiający przegląd udziału danych.":::

1. Wybierz pozycję **Rozpocznij udostępnianie danych**.

1. Wybierz przycisk **Utwórz**.   

1. Podaj szczegółowe informacje o udziale. Określ nazwę, typ udziału, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![Zrzut ekranu przedstawiający szczegóły udziału danych.](./media/enter-share-details.png "Wprowadź szczegóły udziału danych.") 

1. Wybierz opcję **Kontynuuj**.

1. Aby dodać zestawy danych do udziału, wybierz pozycję **Dodaj zestawy danych**. 

    ![Zrzut ekranu przedstawiający sposób dodawania zestawów danych do udziału.](./media/datasets.png "Zestawów danych.")

1. Wybierz typ zestawu danych, który ma zostać dodany. Lista typów zestawów danych zależy od tego, czy w poprzednim kroku wybrano opcję Udostępnianie oparte na migawce czy udostępnianie w miejscu. 

    ![Zrzut ekranu przedstawiający lokalizację, w której ma zostać wybrany typ zestawu danych.](./media/add-datasets.png "Dodaj zestawy danych.")    

1. Przejdź do obiektu, który chcesz udostępnić. Następnie wybierz pozycję **Dodaj zestawy danych**. 

    ![Zrzut ekranu przedstawiający sposób wybierania obiektu do udostępnienia.](./media/select-datasets.png "Wybierz pozycję zestawy danych.")    

1. Na karcie **Adresaci** Dodaj adres e-mail konsumenta danych, wybierając pozycję **Dodaj odbiorcę**. 

    ![Zrzut ekranu przedstawiający sposób dodawania adresów e-mail adresatów.](./media/add-recipient.png "Dodaj adresatów.") 

1. Wybierz opcję **Kontynuuj**.

1. W przypadku wybrania typu udziału migawek można skonfigurować harmonogram migawek, aby zaktualizować dane dla konsumenta danych. 

    ![Zrzut ekranu przedstawiający ustawienia harmonogramu migawek.](./media/enable-snapshots.png "Włącz migawki.") 

1. Wybierz czas rozpoczęcia i interwał cyklu. 

1. Wybierz opcję **Kontynuuj**.

1. Na karcie **Recenzja + tworzenie** przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Następnie wybierz pozycję **Utwórz**.

Twój udział danych platformy Azure został utworzony. Odbiorca Twojego udziału danych może zaakceptować zaproszenie. 

## <a name="receive-data"></a>Odbierz dane

W poniższych sekcjach opisano, jak odbierać udostępnione dane.
### <a name="prerequisites-to-receive-data"></a>Wymagania wstępne dotyczące odbierania danych
Przed zaakceptowaniem zaproszenia do udziału danych upewnij się, że zostały spełnione następujące wymagania wstępne: 

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Zaproszenie z platformy Azure. Temat wiadomości e-mail powinien mieć wartość "zaproszenie do udostępniania danych platformy Azure *\<yourdataprovider\@domain.com>* ".
* Zarejestrowano [dostawcę zasobów Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) w:
    * Subskrypcja platformy Azure, w której utworzysz zasób udziału danych.
    * Subskrypcja platformy Azure, w której znajdują się docelowe magazyny danych platformy Azure.

### <a name="prerequisites-for-a-target-storage-account"></a>Wymagania wstępne dotyczące docelowego konta magazynu

* Konto usługi Azure Storage. [Utwórz konto](../storage/common/storage-account-create.md), jeśli jeszcze go nie masz. 
* Uprawnienie do zapisu na koncie magazynu. To uprawnienie znajduje się w *witrynie Microsoft. Storage/storageAccounts/Write*. Jest częścią roli współautor. 
* Uprawnienie do dodawania przypisania roli do konta magazynu. To przypisanie jest w *firmie Microsoft. Autoryzacja/przypisania ról/zapis*. Jest częścią roli właściciela.  

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Otwieranie zaproszenia

Możesz otworzyć zaproszenie z poczty e-mail lub bezpośrednio z Azure Portal.

1. Aby otworzyć zaproszenie z poczty e-mail, sprawdź skrzynkę odbiorczą dla zaproszenia od dostawcy danych. Zaproszenie z Microsoft Azure jest zatytułowane "zaproszenie do udziału danych platformy Azure z *\<yourdataprovider\@domain.com>* ". Wybierz pozycję **Wyświetl zaproszenie** , aby zobaczyć zaproszenie na platformie Azure. 

   Aby otworzyć zaproszenie z Azure Portal, Wyszukaj *zaproszenia udziału danych*. Zostanie wyświetlona lista zaproszeń do udziału danych.

   ![Zrzut ekranu przedstawiający listę zaproszeń w Azure Portal.](./media/invitations.png "Lista zaproszeń.") 

1. Wybierz udział, który chcesz wyświetlić. 

### <a name="accept-an-invitation"></a>Akceptowanie zaproszenia
1. Przejrzyj wszystkie pola, w tym **warunki użytkowania**. Jeśli akceptujesz warunki, zaznacz pole wyboru. 

   ![Zrzut ekranu przedstawiający obszar Warunki użytkowania.](./media/terms-of-use.png "Warunki użytkowania.") 

1. W obszarze **docelowe konto udziału danych** wybierz subskrypcję i grupę zasobów, w której będziesz wdrażać udział danych. Następnie wypełnij następujące pola:

   * W polu **konto udziału danych** wybierz pozycję **Utwórz nową** , jeśli nie masz konta udziału danych. W przeciwnym razie wybierz istniejące konto udziału danych, które będzie akceptować udział danych. 

   * W polu **Nazwa otrzymanego udziału** pozostaw wartość domyślną określoną przez dostawcę danych lub określ nową nazwę dla odebranego udziału. 

1. Wybierz pozycję **Zaakceptuj i skonfiguruj**. Zostanie utworzona subskrypcja udziału. 

   ![Zrzut ekranu przedstawiający miejsce zaakceptowania opcji konfiguracji.](./media/accept-options.png "Zaakceptuj opcje") 

    Otrzymany udział pojawia się na koncie udziału danych. 

    Jeśli nie chcesz zaakceptować zaproszenia, wybierz pozycję **Odrzuć**. 

### <a name="configure-a-received-share"></a>Skonfiguruj odebrany udział
Wykonaj kroki opisane w tej sekcji, aby skonfigurować lokalizację do odbierania danych.

1. Na karcie **zestawy** danych zaznacz pole wyboru obok elementu DataSet, do którego chcesz przypisać miejsce docelowe. Wybierz pozycję **Mapuj do** celu, aby wybrać docelowy magazyn danych. 

   ![Zrzut ekranu przedstawiający sposób mapowania na element docelowy.](./media/dataset-map-target.png "Mapuj na element docelowy.") 

1. Wybierz docelowy magazyn danych dla danych. Pliki w docelowym magazynie danych, które mają taką samą ścieżkę i nazwę jak pliki w odebranych danych zostaną nadpisywane. 

   ![Zrzut ekranu przedstawiający lokalizację, w której ma zostać wybrane docelowe konto magazynu.](./media/map-target.png "Magazyn docelowy.") 

1. W przypadku udostępniania opartego na migawce, jeśli dostawca danych używa harmonogramu migawek do regularnego aktualizowania danych, można włączyć harmonogram na karcie **harmonogram migawek** . Zaznacz pole obok harmonogramu migawek. Następnie wybierz pozycję **Włącz**. Należy pamiętać, że pierwsza zaplanowana migawka rozpocznie się w ciągu minuty od czasu zaplanowanego, a kolejne migawki rozpocznie się w ciągu kilku sekund od zaplanowanego czasu.

   ![Zrzut ekranu przedstawiający sposób włączania harmonogramu migawek.](./media/enable-snapshot-schedule.png "Włącz harmonogram migawek.")

### <a name="trigger-a-snapshot"></a>Wyzwalanie migawki
Kroki opisane w tej sekcji dotyczą tylko udostępniania opartego na migawce.

1. Migawkę można wyzwolić na karcie **szczegóły** . Na karcie Wybierz pozycję **Wyzwól migawkę**. Możesz wyzwolić pełną migawkę lub przyrostową migawkę danych. Jeśli po raz pierwszy otrzymujesz dane z dostawcy danych, wybierz pozycję **pełna kopia**. Gdy wykonywana jest migawka, kolejne migawki nie rozpocznie się aż do ukończenia poprzedniej.

   ![Zrzut ekranu przedstawiający wybór migawki wyzwalacza.](./media/trigger-snapshot.png "Wyzwalanie migawki.") 

1. Po *pomyślnym* zakończeniu ostatniego uruchomienia Przejdź do docelowego magazynu danych, aby wyświetlić odebrane dane. Wybierz pozycję **zestawy danych**, a następnie wybierz link ścieżka docelowa. 

   ![Zrzut ekranu przedstawiający mapowanie zestawu danych odbiorcy.](./media/consumer-datasets.png "Mapowanie zestawu danych klienta.") 

### <a name="view-history"></a>Wyświetlanie historii
Historię migawek można wyświetlić tylko w ramach udostępniania opartego na migawce. Aby wyświetlić historię, Otwórz kartę **historia** . Tutaj zobaczysz historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 30 dni. 

## <a name="storage-snapshot-performance"></a>Wydajność migawek magazynu
Wydajność migawki magazynu ma wpływ na wiele czynników oprócz liczby plików i rozmiaru udostępnionych danych. Zawsze zaleca się przeprowadzanie własnych testów wydajnościowych. Poniżej przedstawiono przykładowe czynniki wpływające na wydajność.

* Współbieżny dostęp do źródłowych i docelowych magazynów danych.  
* Lokalizacja źródłowych i docelowych magazynów danych. 
* W przypadku migawki przyrostowej liczba plików w udostępnionym zestawie danych może mieć wpływ na czas potrzebny do znalezienia listy plików o ostatniej modyfikacji czasu po ostatniej pomyślnej migawce. 


## <a name="next-steps"></a>Następne kroki
Wiesz już, jak udostępniać i odbierać dane z konta magazynu przy użyciu usługi udziału danych platformy Azure. Aby dowiedzieć się więcej o udostępnianiu z innych źródeł danych, zobacz [obsługiwane magazyny danych](supported-data-stores.md).
---
title: Udostępnianie i odbieranie danych z usług Azure Blob Storage i Azure Data Lake Storage
description: Dowiedz się, jak udostępniać i odbierać dane z Azure Blob Storage i Azure Data Lake Storage.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 59c1ca67c9e93b62890512cda647ffcdf7712f9a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819272"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Udostępnianie i odbieranie danych z usług Azure Blob Storage i Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share obsługuje udostępnianie na podstawie migawek z konta magazynu. W tym artykule wyjaśniono, jak udostępniać i odbierać dane z Azure Blob Storage, Azure Data Lake Storage Gen1 i Azure Data Lake Storage Gen2.

Azure Data Share obsługuje udostępnianie plików, folderów i systemów plików z usług Azure Data Lake Gen1 i Azure Data Lake Gen2. Obsługuje również udostępnianie obiektów blob, folderów i kontenerów z Azure Blob Storage. Możesz udostępniać blokowe, uzupełnialne lub stronicowe obiekty blob, które są odbierane jako blokowe obiekty blob. Dane udostępniane z tych źródeł mogą być odbierane przez usługę Azure Data Lake Gen2 lub Azure Blob Storage.

Gdy systemy plików, kontenery lub foldery są udostępniane w trybie udostępniania opartego na migawkach, użytkownicy danych mogą zdecydować się na tworzenie pełnej kopii danych udziału. Mogą też używać funkcji migawki przyrostowej do kopiowania tylko nowych lub zaktualizowanych plików. Możliwość tworzenia migawek przyrostowych jest oparta na czasie ostatniej modyfikacji plików. 

Istniejące pliki o tej samej nazwie są zastępowane podczas tworzenia migawki. Plik, który został usunięty ze źródła, nie jest usuwany na komputerze docelowym. Puste podfoldery w źródle nie są kopiowane do obiektu docelowego. 

## <a name="share-data"></a>Udostępnianie danych

Skorzystaj z informacji w poniższych sekcjach, aby udostępnić dane przy użyciu Azure Data Share. 
### <a name="prerequisites-to-share-data"></a>Wymagania wstępne dotyczące udostępniania danych

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Znajdź adres e-mail logowania do platformy Azure odbiorcy. Alias adresu e-mail adresata nie będzie działać dla Twoich celów.
* Jeśli źródłowy magazyn danych platformy Azure znajduje się w innej subskrypcji platformy Azure niż ta, w której utworzysz zasób usługi Data Share, zarejestruj dostawcę zasobów [Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji, w której znajduje się magazyn danych platformy Azure. 

### <a name="prerequisites-for-the-source-storage-account"></a>Wymagania wstępne dotyczące źródłowego konta magazynu

* Konto usługi Azure Storage. Jeśli nie masz jeszcze konta, utwórz [je.](../storage/common/storage-account-create.md)
* Uprawnienie do zapisu na koncie magazynu. Uprawnienie do zapisu znajduje się *w microsoft.storage/storageAccounts/write.* Jest on częścią roli Współautor.
* Uprawnienie do dodawania przypisania roli do konta magazynu. To uprawnienie znajduje się w *witrynie Microsoft.Authorization/role assignments/write.* Jest on częścią roli Właściciel. 

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Tworzenie konta Data Share magazynu

Utwórz zasób Azure Data Share w grupie zasobów platformy Azure.

1. W lewym górnym rogu portalu otwórz menu, a następnie wybierz pozycję **Utwórz zasób** (+).

1. Wyszukaj *Data Share*.

1. Wybierz **Data Share** i **utwórz .**

1. Podaj podstawowe szczegóły zasobu Azure Data Share zasobów: 

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure dla konta udziału danych.|
    | Grupa zasobów | *test-resource-group* | Użyj istniejącej grupy zasobów lub utwórz grupę zasobów. |
    | Lokalizacja | *Wschodnie stany USA 2* | Wybierz region dla konta udziału danych.
    | Nazwa | *datashareaccount* | Nadaj nazwę kontu udziału danych. |
    | | |

1. Wybierz **pozycję Przeglądanie + tworzenie**  >  **Utwórz,** aby aprowizować konto udziału danych. Aprowizowanie nowego konta udziału danych zwykle trwa około 2 minut. 

1. Po zakończeniu wdrożenia wybierz pozycję **Przejdź do zasobu**.

### <a name="create-a-share"></a>Tworzenie udziału

1. Przejdź do strony Przegląd **udziału** danych.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Zrzut ekranu przedstawiający omówienie udziału danych.":::

1. Wybierz **pozycję Rozpocznij udostępnianie danych.**

1. Wybierz przycisk **Utwórz**.   

1. Podaj szczegóły udziału. Określ nazwę, typ udziału, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![Zrzut ekranu przedstawiający szczegóły udziału danych.](./media/enter-share-details.png "Wprowadź szczegóły udziału danych.") 

1. Wybierz opcję **Kontynuuj**.

1. Aby dodać zestawy danych do udziału, wybierz **pozycję Dodaj zestawy danych.** 

    ![Zrzut ekranu przedstawiający sposób dodawania zestawów danych do udziału.](./media/datasets.png "Zestawach danych.")

1. Wybierz typ zestawu danych do dodania. Lista typów zestawów danych zależy od tego, czy w poprzednim kroku wybrano udostępnianie oparte na migawkach, czy udostępnianie w miejscu. 

    ![Zrzut ekranu przedstawiający miejsce wybierania typu zestawu danych.](./media/add-datasets.png "Dodawanie zestawów danych.")    

1. Przejdź do obiektu, który chcesz udostępnić. Następnie wybierz **pozycję Dodaj zestawy danych.** 

    ![Zrzut ekranu przedstawiający sposób wybierania obiektu do udostępnienia.](./media/select-datasets.png "Wybierz pozycję Zestawy danych.")    

1. Na karcie **Adresaci** dodaj adres e-mail użytkownika danych, wybierając pozycję **Dodaj adresata.** 

    ![Zrzut ekranu przedstawiający sposób dodawania adresów e-mail adresata.](./media/add-recipient.png "Dodaj adresatów.") 

1. Wybierz opcję **Kontynuuj**.

1. Jeśli wybrano typ udziału migawki, możesz skonfigurować harmonogram migawek w celu zaktualizowania danych użytkownika danych. 

    ![Zrzut ekranu przedstawiający ustawienia harmonogramu migawek.](./media/enable-snapshots.png "Włączanie migawek.") 

1. Wybierz czas rozpoczęcia i interwał cyklu. 

1. Wybierz opcję **Kontynuuj**.

1. Na karcie **Przeglądanie + tworzenie** przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Następnie wybierz pozycję **Utwórz**.

Udział danych platformy Azure został utworzony. Odbiorca udziału danych może zaakceptować Zaproszenie. 

## <a name="receive-data"></a>Odbieranie danych

W poniższych sekcjach opisano sposób odbierania danych udostępnionych.
### <a name="prerequisites-to-receive-data"></a>Wymagania wstępne dotyczące odbierania danych
Przed zaakceptowaniem zaproszenia do udziału danych upewnij się, że masz następujące wymagania wstępne: 

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, utwórz bezpłatne [konto](https://azure.microsoft.com/free/).
* Zaproszenie z platformy Azure. Temat wiadomości e-mail powinien być "Azure Data Share z *\<yourdataprovider\@domain.com>* ".
* Zarejestrowany dostawca [zasobów Microsoft.DataShare w:](concepts-roles-permissions.md#resource-provider-registration)
    * Subskrypcja platformy Azure, w której utworzysz zasób Data Share zasobów.
    * Subskrypcja platformy Azure, w której znajdują się docelowe magazyny danych platformy Azure.

### <a name="prerequisites-for-a-target-storage-account"></a>Wymagania wstępne dotyczące docelowego konta magazynu

* Konto usługi Azure Storage. Jeśli jeszcze go nie masz, [utwórz konto](../storage/common/storage-account-create.md). 
* Uprawnienie do zapisu na koncie magazynu. To uprawnienie znajduje się w *microsoft.storage/storageAccounts/write.* Jest on częścią roli Współautor. 
* Uprawnienie do dodawania przypisania roli do konta magazynu. To przypisanie znajduje się w *witrynie Microsoft.Authorization/role assignments/write.* Jest on częścią roli Właściciel.  

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

### <a name="open-an-invitation"></a>Otwieranie zaproszenia

Zaproszenie możesz otworzyć za pomocą wiadomości e-mail lub bezpośrednio z Azure Portal.

1. Aby otworzyć zaproszenie z wiadomości e-mail, sprawdź w skrzynce odbiorczej zaproszenie od dostawcy danych. Zaproszenie z Microsoft Azure ma tytuł "Azure Data Share z *\<yourdataprovider\@domain.com>* ". Wybierz **pozycję Wyświetl zaproszenie,** aby wyświetlić zaproszenie na platformie Azure. 

   Aby otworzyć zaproszenie z Azure Portal, wyszukaj Data Share *zaproszenia.* Zostanie wyświetlona lista Data Share zaproszenia.

   ![Zrzut ekranu przedstawiający listę zaproszeń w Azure Portal.](./media/invitations.png "Lista zaproszeń.") 

1. Wybierz udział, który chcesz wyświetlić. 

### <a name="accept-an-invitation"></a>Akceptowanie zaproszenia
1. Przejrzyj wszystkie pola, w tym **Warunki użytkowania**. Jeśli akceptujesz warunki, zaznacz pole wyboru. 

   ![Zrzut ekranu przedstawiający obszar Warunki użytkowania danych.](./media/terms-of-use.png "Warunki użytkowania.") 

1. W **obszarze Data Share docelowego** wybierz subskrypcję i grupę zasobów, w której będziesz wdrażać Data Share. Następnie wypełnij następujące pola:

   * W polu **Konto udziału** danych wybierz pozycję **Utwórz nowe,** jeśli nie masz konta Data Share danych. W przeciwnym razie wybierz istniejące Data Share, które będzie akceptować Twój udział danych. 

   * W polu **Odebrano nazwę udziału** pozostaw wartość domyślną określoną przez dostawcę danych lub określ nową nazwę odebranego udziału. 

1. Wybierz **pozycję Zaakceptuj i skonfiguruj**. Zostanie utworzona subskrypcja udziału. 

   ![Zrzut ekranu przedstawiający miejsce akceptowania opcji konfiguracji.](./media/accept-options.png "Zaakceptuj opcje") 

    Odebrany udział zostanie wyświetlony na Data Share konta. 

    Jeśli nie chcesz akceptować zaproszenia, wybierz pozycję **Odrzuć**. 

### <a name="configure-a-received-share"></a>Konfigurowanie odebranego udziału
Wykonaj kroki opisane w tej sekcji, aby skonfigurować lokalizację do odbierania danych.

1. Na karcie **Zestawy** danych zaznacz pole wyboru obok zestawu danych, do którego chcesz przypisać miejsce docelowe. Wybierz **pozycję Mapuj do celu,** aby wybrać docelowy magazyn danych. 

   ![Zrzut ekranu przedstawiający sposób mapowania na element docelowy.](./media/dataset-map-target.png "Mapowanie na element docelowy.") 

1. Wybierz docelowy magazyn danych dla danych. Pliki w docelowym magazynie danych, które mają taką samą ścieżkę i nazwę jak pliki w odebranych danych, zostaną zastąpione. 

   ![Zrzut ekranu przedstawiający miejsce wybierania docelowego konta magazynu.](./media/map-target.png "Magazyn docelowy.") 

1. W przypadku udostępniania opartego na migawkach, jeśli dostawca danych używa harmonogramu migawek do regularnego aktualizowania danych, harmonogram można włączyć na karcie **Harmonogram migawek.** Zaznacz pole wyboru obok harmonogramu migawek. Następnie wybierz pozycję **Włącz.** Należy pamiętać, że pierwsza zaplanowana migawka zostanie uruchamiana w ciągu jednej minuty od harmonogramu, a kolejne migawki będą rozpoczynać się w ciągu sekund od zaplanowanego czasu.

   ![Zrzut ekranu przedstawiający sposób włączania harmonogramu migawek.](./media/enable-snapshot-schedule.png "Włącz harmonogram migawek.")

### <a name="trigger-a-snapshot"></a>Wyzwalanie migawki
Kroki opisane w tej sekcji dotyczą tylko udostępniania opartego na migawkach.

1. Migawkę można wyzwolić z **karty Szczegóły.** Na karcie wybierz pozycję **Wyzwolij migawkę**. Możesz wyzwolić pełną migawkę lub przyrostową migawkę danych. Jeśli po raz pierwszy otrzymujesz dane od dostawcy danych, wybierz pozycję **Pełna kopia.** Podczas wykonywania migawki kolejne migawki nie będą rozpoczynane do momentu ukończenia poprzedniego.

   ![Zrzut ekranu przedstawiający wybór migawki wyzwalacza.](./media/trigger-snapshot.png "Wyzwalanie migawki.") 

1. Po pomyślnym ostatnim uruchomieniu *przejdź* do docelowego magazynu danych, aby wyświetlić odebrane dane. Wybierz **pozycję Zestawy** danych, a następnie wybierz link ścieżki docelowej. 

   ![Zrzut ekranu przedstawiający mapowanie zestawu danych odbiorców.](./media/consumer-datasets.png "Mapowanie zestawu danych odbiorców.") 

### <a name="view-history"></a>Wyświetlanie historii
Historię migawek można wyświetlać tylko w przypadku udostępniania opartego na migawkach. Aby wyświetlić historię, otwórz **kartę** Historia. W tym miejscu zobaczysz historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 30 dni. 

## <a name="storage-snapshot-performance"></a>Wydajność migawek magazynu
Na wydajność migawek magazynu oprócz liczby plików i rozmiaru udostępnionych danych ma wpływ wiele czynników. Zawsze zaleca się przeprowadzanie własnych testów wydajnościowych. Poniżej przedstawiono niektóre przykładowe czynniki wpływające na wydajność.

* Współbieżny dostęp do źródłowych i docelowych magazynów danych.  
* Lokalizacja źródłowych i docelowych magazynów danych. 
* W przypadku migawki przyrostowej liczba plików w udostępnionym zestawie danych może mieć wpływ na czas, jaki zajmuje znalezienie listy plików z czasem ostatniej modyfikacji po ostatniej pomyślnej migawce. 


## <a name="next-steps"></a>Następne kroki
Wiesz już, jak udostępniać i odbierać dane z konta magazynu przy użyciu usługi Azure Data Share magazynu. Aby dowiedzieć się więcej o udostępnianiu z innych źródeł danych, zobacz [Obsługiwane magazyny danych.](supported-data-stores.md)

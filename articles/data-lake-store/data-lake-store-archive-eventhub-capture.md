---
title: Przechwyć dane z Event Hubs do Azure Data Lake Storage Gen1
description: Dowiedz się, jak używać Azure Data Lake Storage Gen1 do przechwytywania danych odebranych przez usługę Azure Event Hubs. Zacznij od sprawdzenia wymagań wstępnych.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c5254558c62499ed6864e809dbc93c26ebba94a9
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190273"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Użyj Azure Data Lake Storage Gen1 do przechwytywania danych z Event Hubs

Dowiedz się, jak używać Azure Data Lake Storage Gen1 do przechwytywania danych odebranych przez usługę Azure Event Hubs.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz Wprowadzenie [do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Przestrzeń nazw Event Hubs**. Aby uzyskać instrukcje, zobacz [Tworzenie przestrzeni nazw Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Upewnij się, że konto Data Lake Storage Gen1 i przestrzeń nazw Event Hubs znajdują się w tej samej subskrypcji platformy Azure.


## <a name="assign-permissions-to-event-hubs"></a>Przypisywanie uprawnień do Event Hubs

W tej sekcji utworzysz folder na koncie, w którym chcesz przechwytywać dane z Event Hubs. Możesz również przypisać uprawnienia do Event Hubs, aby można było zapisywać dane na koncie Data Lake Storage Gen1. 

1. Otwórz konto Data Lake Storage Gen1, do którego chcesz przechwytywać dane Event Hubs a następnie kliknij przycisk **Eksplorator danych**.

    ![Eksplorator danych Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Eksplorator danych Data Lake Storage Gen1")

1.  Kliknij pozycję **Nowy folder** , a następnie wprowadź nazwę folderu, w którym chcesz przechwycić dane.

    ![Utwórz nowy folder w Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Utwórz nowy folder w Data Lake Storage Gen1")

1. Przypisz uprawnienia w katalogu głównym Data Lake Storage Gen1. 

    a. Kliknij **Eksplorator danych**, wybierz katalog główny konta Data Lake Storage Gen1, a następnie kliknij pozycję **dostęp**.

    ![Przypisywanie uprawnień dla Data Lake Storage Gen1 głównego](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Przypisywanie uprawnień dla Data Lake Storage Gen1 głównego")

    b. W obszarze **dostęp**kliknij pozycję **Dodaj**, kliknij pozycję **Wybierz użytkownika lub grupę**, a następnie wyszukaj ciąg `Microsoft.EventHubs` . 

    ![Przypisywanie uprawnień dla Data Lake Storage Gen1 głównego](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Przypisywanie uprawnień dla Data Lake Storage Gen1 głównego")
    
    Kliknij pozycję **Wybierz**.

    c. W obszarze **przypisywanie uprawnień**kliknij przycisk **Wybierz uprawnienia**. Ustaw **uprawnienia** do **wykonania**. Ustaw wartość opcji **Dodaj do** na **ten folder i wszystkie elementy podrzędne**. Ustaw pozycję **Dodaj jako jako** **uprawnienia dostępu i domyślny wpis uprawnienia**.

    > [!IMPORTANT]
    > Podczas tworzenia nowej hierarchii folderów do przechwytywania danych odbieranych przez usługę Azure Event Hubs jest to prosty sposób zapewnienia dostępu do folderu docelowego.  Jednak dodanie uprawnień do wszystkich elementów podrzędnych folderu najwyższego poziomu z wieloma plikami podrzędnymi i folderami może zająć dużo czasu.  Jeśli folder główny zawiera dużą liczbę plików i folderów, można szybciej dodawać uprawnienia do **wykonywania** dla `Microsoft.EventHubs` poszczególnych folderów w ścieżce do folderu docelowego. 

    ![Przypisywanie uprawnień dla Data Lake Storage Gen1 głównego](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Przypisywanie uprawnień dla Data Lake Storage Gen1 głównego")

    Kliknij przycisk **OK**.

1. Przypisz uprawnienia do folderu na koncie Data Lake Storage Gen1, w którym chcesz przechwytywać dane.

    a. Kliknij pozycję **Eksplorator danych**, wybierz folder na koncie Data Lake Storage Gen1, a następnie kliknij pozycję **dostęp**.

    ![Przypisywanie uprawnień do folderu Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Przypisywanie uprawnień do folderu Data Lake Storage Gen1")

    b. W obszarze **dostęp**kliknij pozycję **Dodaj**, kliknij pozycję **Wybierz użytkownika lub grupę**, a następnie wyszukaj ciąg `Microsoft.EventHubs` . 

    ![Przypisywanie uprawnień do folderu Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Przypisywanie uprawnień do folderu Data Lake Storage Gen1")
    
    Kliknij pozycję **Wybierz**.

    c. W obszarze **przypisywanie uprawnień**kliknij przycisk **Wybierz uprawnienia**. Ustawianie **uprawnień** do **odczytu, zapisu** i **wykonania**. Ustaw wartość opcji **Dodaj do** na **ten folder i wszystkie elementy podrzędne**. Na koniec ustaw pozycję **Dodaj jako jako** **uprawnienia dostępu i domyślny wpis uprawnienia**.

    ![Przypisywanie uprawnień do folderu Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Przypisywanie uprawnień do folderu Data Lake Storage Gen1")
    
    Kliknij przycisk **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Konfigurowanie Event Hubs przechwytywania danych do Data Lake Storage Gen1

W tej sekcji utworzysz centrum zdarzeń w przestrzeni nazw Event Hubs. Należy również skonfigurować centrum zdarzeń do przechwytywania danych do konta Azure Data Lake Storage Gen1. W tej sekcji założono, że utworzono już Event Hubs przestrzeń nazw.

1. W okienku **Przegląd** obszaru nazw Event Hubs kliknij pozycję **+ centrum zdarzeń**.

    ![Tworzenie centrum zdarzeń](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Tworzenie centrum zdarzeń")

1. Podaj następujące wartości, aby skonfigurować Event Hubs przechwytywania danych do Data Lake Storage Gen1.

    ![Tworzenie centrum zdarzeń](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Tworzenie centrum zdarzeń")

    a. Podaj nazwę centrum zdarzeń.
    
    b. Na potrzeby tego samouczka Ustaw **liczbę partycji** i **przechowywanie komunikatów** na wartości domyślne.
    
    c. Ustaw wartość opcji **Przechwyć** **na na**. Ustaw **przedział czasu** (Częstotliwość przechwytywania) i **rozmiar okna** (rozmiar danych do przechwycenia). 
    
    d. W obszarze **dostawca przechwytywania**wybierz pozycję **Azure Data Lake Store** a następnie wybierz utworzone wcześniej konto Data Lake Storage Gen1. W polu **ścieżka do Data Lake**wprowadź nazwę folderu utworzonego na koncie Data Lake Storage Gen1. Wystarczy podać ścieżkę względną do folderu.

    e. Pozostaw **przykładowe nazwy plików przechwytywania** do wartości domyślnej. Ta opcja reguluje strukturę folderów utworzoną w folderze przechwytywania.

    f. Kliknij przycisk **Utwórz**.

## <a name="test-the-setup"></a>Testowanie konfiguracji

Teraz można testować rozwiązanie, wysyłając dane do centrum zdarzeń platformy Azure. Postępuj zgodnie z instrukcjami wyświetlanymi na stronie [wysyłanie zdarzeń do usługi Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Po rozpoczęciu wysyłania danych zostaną wyświetlone dane odzwierciedlone w Data Lake Storage Gen1 przy użyciu określonej struktury folderów. Na przykład zostanie wyświetlona struktura folderów, jak pokazano na poniższym zrzucie ekranu, na koncie Data Lake Storage Gen1.

![Przykładowe dane EventHub w Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Przykładowe dane EventHub w Data Lake Storage Gen1")

> [!NOTE]
> Nawet jeśli nie masz komunikatów do Event Hubs, Event Hubs zapisuje puste pliki z tylko nagłówkami w ramach konta Data Lake Storage Gen1. Pliki są zapisywane w tym samym przedziale czasu, który został dostarczony podczas tworzenia Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analizowanie danych w Data Lake Storage Gen1

Gdy dane są w Data Lake Storage Gen1, można uruchamiać zadania analityczne, aby przetwarzać i przetwarzanie dane. Zobacz [przykład USQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) , aby dowiedzieć się, jak to zrobić za pomocą Azure Data Lake Analytics.
  

## <a name="see-also"></a>Zobacz też
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Kopiowanie danych z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

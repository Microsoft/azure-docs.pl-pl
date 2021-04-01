---
title: Integracja Data Lake Storage Gen1 z usługą Azure Data Catalog
description: Dowiedz się, jak zarejestrować dane z Azure Data Lake Storage Gen1 w Azure Data Catalog, aby umożliwić odnajdywanie danych w organizacji.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 02544489816f5711ca6e599c2bce03737c747934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106624"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Rejestruj dane z Azure Data Lake Storage Gen1 w Azure Data Catalog
W tym artykule dowiesz się, jak zintegrować Azure Data Lake Storage Gen1 z Azure Data Catalog, aby umożliwić odnajdywanie danych w organizacji przez integrację jej z Data Catalog. Aby uzyskać więcej informacji na temat katalogowania danych, zobacz [Azure Data Catalog](../data-catalog/overview.md). Aby zrozumieć scenariusze, w których można używać Data Catalog, zobacz [Azure Data Catalog typowych scenariuszach](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Włącz subskrypcję platformy Azure** , aby uzyskać Data Lake Storage Gen1. Zobacz [instrukcje](data-lake-store-get-started-portal.md).
* **Konto Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w punkcie wprowadzenie [do Azure Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md). Na potrzeby tego samouczka Utwórz konto Data Lake Storage Gen1 o nazwie **datacatalogstore**.

    Po utworzeniu konta Przekaż do niego przykładowe dane. W tym samouczku przekażemy nam wszystkie pliki CSV w folderze **AmbulanceData** w [repozytorium Azure Data Lake git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Aby przekazać dane do kontenera obiektów blob, można użyć różnych klientów, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).
* **Azure Data Catalog**. Twoja organizacja musi mieć już Azure Data Catalog utworzone dla Twojej organizacji. Dla każdej organizacji dozwolony jest tylko jeden wykaz.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Zarejestruj Data Lake Storage Gen1 jako źródło dla Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Przejdź do `https://azure.microsoft.com/services/data-catalog` , a następnie kliknij pozycję **Rozpocznij pracę**.
1. Zaloguj się do portalu usługi Azure Data Catalog, a następnie kliknij pozycję **Publikuj dane**.

    ![Rejestrowanie źródła danych](./media/data-lake-store-with-data-catalog/register-data-source.png "Rejestrowanie źródła danych")
1. Na następnej stronie kliknij pozycję **Uruchom aplikację**. Spowoduje to pobranie pliku manifestu aplikacji na komputerze. Kliknij dwukrotnie plik manifestu, aby uruchomić aplikację.
1. Na stronie powitalnej kliknij przycisk **Zaloguj** i wprowadź swoje poświadczenia.

    ![Ekran powitalny](./media/data-lake-store-with-data-catalog/welcome.screen.png "Ekran powitalny")
1. Na stronie Wybierz źródło danych wybierz pozycję **Azure Data Lake Store**, a następnie kliknij przycisk **dalej**.

    ![Wybieranie źródła danych](./media/data-lake-store-with-data-catalog/select-source.png "Wybieranie źródła danych")
1. Na następnej stronie Podaj nazwę konta Data Lake Storage Gen1, które chcesz zarejestrować w programie Data Catalog. Pozostaw inne opcje jako domyślne, a następnie kliknij przycisk **Połącz**.

    ![Łączenie ze źródłem danych](./media/data-lake-store-with-data-catalog/connect-to-source.png "Łączenie ze źródłem danych")
1. Następną stronę można podzielić na następujące segmenty.

    a. Pole **Hierarchia serwerów** reprezentuje strukturę folderu konta Data Lake Storage Gen1. **$Root** reprezentuje katalog główny konta Data Lake Storage Gen1, a **AmbulanceData** reprezentuje folder utworzony w katalogu głównym konta Data Lake Storage Gen1.

    b. Pole **dostępne obiekty** zawiera listę plików i folderów znajdujących się w folderze **AmbulanceData** .

    c. Pole **obiekty do zarejestrowania** zawiera listę plików i folderów, które mają zostać zarejestrowane w Azure Data Catalog.

    ![Zrzut ekranu przedstawiający okno dialogowe Microsoft Azure Data Catalog — konto magazynu.](./media/data-lake-store-with-data-catalog/view-data-structure.png "Wyświetl strukturę danych")
1. Na potrzeby tego samouczka należy zarejestrować wszystkie pliki w katalogu. W tym celu kliknij przycisk (![Przenieś obiekty](./media/data-lake-store-with-data-catalog/move-objects.png "Przenoszenie obiektów")), aby przenieść wszystkie pliki do pola **zarejestrowano** .

    Ponieważ dane zostaną zarejestrowane w wykazie danych w całej organizacji, to zalecane podejście do dodawania metadanych, których można później użyć do szybkiego lokalizowania danych. Na przykład możesz dodać adres e-mail właściciela danych (na przykład jeden, kto przekazuje dane) lub dodać tag w celu zidentyfikowania danych. Poniższy zrzut ekranu przedstawia tag, który został dodany do danych.

    ![Zrzut ekranu przedstawiający okno dialogowe Microsoft Azure konta magazynu Data Catalog z tagiem, który został dodany do danych o nazwie.](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Wyświetl strukturę danych")

    Kliknij pozycję **Zarejestruj**.
1. Poniższy zrzut ekranu oznacza, że dane zostały pomyślnie zarejestrowane w Data Catalog.

    ![Zakończono rejestrację](./media/data-lake-store-with-data-catalog/registration-complete.png "Wyświetl strukturę danych")
1. Kliknij pozycję **Wyświetl Portal** , aby wrócić do portalu Data Catalog i sprawdź, czy możesz teraz uzyskać dostęp do zarejestrowanych danych z portalu. Aby wyszukać dane, możesz użyć znacznika użytego podczas rejestrowania danych.

     ![Wyszukaj dane w wykazie](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Wyszukaj dane w wykazie")
1. Teraz można wykonywać operacje, takie jak dodawanie adnotacji i dokumentacji do danych. Aby uzyskać więcej informacji, zobacz następujące linki.

    * [Dodawanie adnotacji do źródeł danych w Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Dokumentowanie źródeł danych w Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Zobacz też
* [Dodawanie adnotacji do źródeł danych w Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Dokumentowanie źródeł danych w Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integracja Data Lake Storage Gen1 z innymi usługami platformy Azure](data-lake-store-integrate-with-other-services.md)
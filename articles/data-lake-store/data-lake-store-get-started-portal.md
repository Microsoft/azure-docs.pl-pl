---
title: Wprowadzenie do Azure Data Lake Storage Gen1 — Portal
description: Użyj Azure Portal, aby utworzyć konto Data Lake Storage Gen1 i wykonać podstawowe operacje na koncie.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 428ed96c3223e644b0c78712723231a5fabbdc77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91578584"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Wprowadzenie do Azure Data Lake Storage Gen1 przy użyciu Azure Portal

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Program PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Dowiedz się, jak za pomocą Azure Portal utworzyć konto Data Lake Storage Gen1 i wykonać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych, usuwanie konta itp. Aby uzyskać więcej informacji, zobacz [omówienie Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Data Lake Storage Gen1

1. Zaloguj się do nowego [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób > > magazynu Data Lake Storage Gen1**.
3. W bloku **nowy Data Lake Storage Gen1** podaj wartości, jak pokazano na poniższym zrzucie ekranu:

    ![Utwórz nowe konto Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Utwórz nowe konto Data Lake Storage Gen1")

   * **Nazwa**. Wprowadź unikatową nazwę konta Data Lake Storage Gen1.
   * **Subskrypcja**. Wybierz subskrypcję, w ramach której chcesz utworzyć nowe konto Data Lake Storage Gen1.
   * **Grupa zasobów**. Wybierz istniejącą grupę zasobów lub użyj opcji **Utwórz nową**, aby utworzyć taką grupę. Grupa zasobów to kontener, który zawiera powiązane zasoby dla aplikacji. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](../azure-resource-manager/management/overview.md#resource-groups).
   * **Lokalizacja**: Wybierz lokalizację, w której chcesz utworzyć konto Data Lake Storage Gen1.
   * **Ustawienia szyfrowania**. Dostępne są trzy opcje:

     * **Nie włączaj szyfrowania**.
     * **Użyj kluczy zarządzanych przez Data Lake Storage Gen1**, jeśli chcesz, aby Data Lake Storage Gen1 do zarządzania kluczami szyfrowania.
     * **Użyj kluczy z własnej usługi Key Vault**. Możesz wybrać istniejącą usługę Azure Key Vault lub utworzyć nową usługę Key Vault. Aby użyć kluczy z Key Vault, należy przypisać uprawnienia do konta Data Lake Storage Gen1, aby uzyskać dostęp do Azure Key Vault. Aby uzyskać instrukcje, zobacz [Przypisywanie uprawnień do usługi Azure Key Vault](#assign-permissions-to-azure-key-vault).

        ![Zrzut ekranu przedstawiający nowy blok Data Lake Storage Gen 1 i blok ustawienia szyfrowania.](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Szyfrowanie Data Lake Storage Gen1")

        Kliknij przycisk **OK** w bloku **Ustawienia szyfrowania**.

        Aby uzyskać więcej informacji, zobacz [szyfrowanie danych w Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Kliknij pozycję **Utwórz**. Jeśli wybrano opcję przypięcia konta do pulpitu nawigacyjnego, nastąpi powrót do pulpitu nawigacyjnego i zobaczysz postęp aprowizacji Data Lake Storage Gen1 konta. Po zainicjowaniu obsługi konta Data Lake Storage Gen1 zostanie wyświetlony blok konta.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Przypisywanie uprawnień do usługi Azure Key Vault

W przypadku użycia kluczy z Azure Key Vault w celu skonfigurowania szyfrowania na koncie Data Lake Storage Gen1 należy skonfigurować dostęp między kontem Data Lake Storage Gen1 i kontem Azure Key Vault. W tym celu wykonaj poniższe kroki.

1. Jeśli użyto kluczy z Azure Key Vault, w bloku dla konta Data Lake Storage Gen1 zostanie wyświetlone ostrzeżenie u góry. Kliknij ostrzeżenie, aby otworzyć obszar **Szyfrowanie**.

    ![Zrzut ekranu przedstawiający blok konta Data Lake Storage Gen1, w którym znajduje się ostrzeżenie informujące o tym, że wymagana jest konfiguracja uprawnień magazynu kluczy. Kliknij tutaj, aby skonfigurować.](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Szyfrowanie Data Lake Storage Gen1")
2. Blok zawiera dwie opcje umożliwiające skonfigurowanie dostępu.

    ![Zrzut ekranu przedstawiający blok szyfrowania.](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Szyfrowanie Data Lake Storage Gen1")

   * W pierwszej opcji kliknij pozycję **Udziel uprawnień** w celu skonfigurowania dostępu. Pierwsza opcja jest włączona tylko wtedy, gdy użytkownik, który utworzył konto Data Lake Storage Gen1, jest również administratorem dla Azure Key Vault.
   * Druga opcja polega na uruchomieniu polecenia cmdlet programu PowerShell wyświetlonego w bloku. Musisz być właścicielem usługi Azure Key Vault lub mieć możliwość udzielania uprawnień w usłudze Azure Key Vault. Po uruchomieniu polecenia cmdlet wróć do bloku i kliknij przycisk **Włącz**, aby skonfigurować dostęp.

> [!NOTE]
> Konto Data Lake Storage Gen1 można również utworzyć przy użyciu szablonów Azure Resource Manager. Te szablony są dostępne na stronie [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> * Bez szyfrowania danych: [wdróż Azure Data Lake Storage Gen1 konto bez szyfrowania danych](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * Z szyfrowaniem danych przy użyciu Data Lake Storage Gen1: [Wdróż konto Data Lake Storage Gen1 z szyfrowaniem (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Z szyfrowaniem danych przy użyciu Azure Key Vault: [Wdróż konto Data Lake Storage Gen1 z szyfrowaniem (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Tworzenie folderów

Możesz tworzyć foldery na koncie Data Lake Storage Gen1, aby zarządzać danymi i ich przechowywać.

1. Otwórz utworzone konto Data Lake Storage Gen1. W okienku po lewej stronie kliknij pozycję **wszystkie zasoby**, a następnie w bloku **wszystkie zasoby** kliknij nazwę konta, pod którym chcesz utworzyć foldery. Jeśli konto jest przypięte do tablicy startowej, kliknij kafelek konta.
2. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Eksplorator danych**.

    ![Zrzut ekranu przedstawiający blok konta Data Lake Storage generacji 1 z opcją Eksplorator danych o nazwie out.](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Tworzenie folderów na koncie Data Lake Storage Gen1")
3. W bloku Eksplorator danych kliknij pozycję **Nowy folder**, wprowadź nazwę nowego folderu, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający blok Eksplorator danych z opcją nowy folder i pole tekstowe Utwórz nowy folder o nazwie wychodzące.](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Tworzenie folderów na koncie Data Lake Storage Gen1")

    Nowo utworzony folder jest widoczny na liście w bloku **Eksplorator danych**. Foldery zagnieżdżone można tworzyć na dowolnym poziomie.

    ![Tworzenie folderów na koncie Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Tworzenie folderów na koncie Data Lake")

## <a name="upload-data"></a><a name="uploaddata"></a>Przekazywanie danych

Dane można przekazać do konta Data Lake Storage Gen1 bezpośrednio na poziomie głównym lub do folderu utworzonego w ramach konta.

1. W bloku **Eksplorator danych** kliknij przycisk **Przekaż**.
2. W bloku **Przekaż pliki** przejdź do plików, które chcesz przekazać, a następnie kliknij pozycję **Dodaj wybrane pliki**. Możesz też wybrać więcej niż jeden plik do przekazania.

    ![Przekazywanie danych](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Przekazywanie danych")

Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>Akcje dostępne dla przechowywanych danych

Kliknij ikonę wielokropka dla pliku, a następnie w menu podręcznym kliknij akcję, którą chcesz wykonać na danych.

![Właściwości danych](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Właściwości danych")

## <a name="secure-your-data"></a>Zabezpieczanie danych

Dane przechowywane na koncie usługi Data Lake Storage Gen1 można zabezpieczyć za pomocą Azure Active Directory i kontroli dostępu (ACL). Aby uzyskać instrukcje, jak to zrobić, zobacz [Zabezpieczanie danych w Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Usuń konto

Aby usunąć konto Data Lake Storage Gen1, w bloku Data Lake Storage Gen1 kliknij pozycję **Usuń**. W celu potwierdzenia akcji zostanie wyświetlony monit o wprowadzenie nazwy konta, które chcesz usunąć. Wprowadź nazwę konta, a następnie kliknij pozycję **Usuń**.

![Usuń konto Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Usuwanie konta usługi Data Lake")

## <a name="next-steps"></a>Następne kroki

* [Użyj Azure Data Lake Storage Gen1, aby uzyskać wymagania dotyczące danych Big Data](data-lake-store-data-scenarios.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Użyj Azure Data Lake Analytics z Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

---
title: 'Samouczek: skanowanie danych za pomocą usługi Azure kontrolą (wersja zapoznawcza)'
description: W tym samouczku uruchomisz zestaw startowy w celu skonfigurowania danych, a następnie przeskanujesz dane ze źródeł danych w katalogu usługi Azure kontrolą.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: eafa2187308c0324b85596ce25e8310fd8506a97
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249606"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>Samouczek: skanowanie danych za pomocą usługi Azure kontrolą (wersja zapoznawcza)

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

W tej *serii pięciu części samouczków* nauczysz się, jak zarządzać zarządzaniem danymi za pomocą usługi Azure kontrolą (wersja zapoznawcza). W pozostałej części tego samouczka zostanie użyta nieruchomość danych utworzona w tym temacie.

W części 1 tej serii samouczków będziesz:

> [!div class="checklist"]
>
> * Utwórz nieruchomość danych przy użyciu różnych zasobów danych platformy Azure.
> * Skanuj dane w wykazie.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Konto usługi Azure kontrolą](create-catalog-portal.md).
* [Zestaw startowy](https://download.microsoft.com/download/9/7/9/979db3b1-0916-4997-a7fb-24e3d8f83174/PurviewStarterKitV4.zip) , który będzie wdrażał dane.

> [!NOTE]
> Zestaw startowy jest dostępny tylko dla systemu Windows.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-data-estate"></a>Tworzenie danych

W tej sekcji uruchamiane są skrypty startowe programu w celu utworzenia symulowanej postaci danych. Nieruchomość danych to portfolio wszystkich danych należących do firmy. Skrypt zestawu startowego wykonuje następujące czynności:

* Tworzy konto usługi Azure Blob Storage i wypełnia konto danymi.
* Tworzy konto Azure Data Lake Storage Gen2.
* Tworzy wystąpienie Azure Data Factory i kojarzy wystąpienie z usługą Azure kontrolą.
* Konfiguruje i wyzwala potok działania kopiowania między kontami usługi Azure Blob Storage i Azure Data Lake Storage Gen2.
* Wypchnij skojarzone elementy powiązane z Azure Data Factory do usługi Azure kontrolą.

### <a name="prepare-to-run-the-starter-kit"></a>Przygotowanie do uruchomienia zestawu startowego

Wykonaj następujące kroki, aby skonfigurować oprogramowanie klienckie zestawu startowego na komputerze z systemem Windows:

1. [Pobierz zestaw startowy](https://download.microsoft.com/download/9/7/9/979db3b1-0916-4997-a7fb-24e3d8f83174/PurviewStarterKitV4.zip)i Wyodrębnij jego zawartość do wybranej lokalizacji.


1. Na komputerze wprowadź program **PowerShell** w polu wyszukiwania na pasku zadań systemu Windows. Na liście wyszukiwania kliknij prawym przyciskiem myszy pozycję **Windows PowerShell**, a następnie wybierz polecenie **Uruchom jako administrator**.

1. W oknie programu PowerShell wprowadź następujące polecenie, zastępując `<path-to-starter-kit>` je ścieżką folderu wyodrębnionych plików startowych.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. Wprowadź następujące polecenie, aby zainstalować polecenia cmdlet platformy Azure.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. Jeśli zobaczysz monit ostrzegawczy, *dostawca NuGet jest wymagany do kontynuowania*, wprowadź **Y**, a następnie naciśnij klawisz ENTER.

1. Jeśli zobaczysz monit ostrzegawczy, *niezaufane repozytorium* **, wprowadź a, a następnie** naciśnij klawisz ENTER.

Zainstalowanie wymaganych modułów może potrwać do minuty.

### <a name="collect-data-needed-to-run-the-scripts"></a>Zbierz dane, które są konieczne do uruchomienia skryptów

Przed uruchomieniem skryptów programu PowerShell w celu uruchomienia wykazu, Pobierz wartości następujących argumentów do użycia w skryptach:

* TenantID
   1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory**.
   1. W sekcji **Zarządzanie** w okienku nawigacji po lewej stronie wybierz pozycję **Właściwości**. Następnie wybierz ikonę kopiowania dla **identyfikatora dzierżawy** , aby zapisać wartość w Schowku. Wklej wartość w edytorze tekstu do późniejszego użycia.

* Identyfikator
   1. W Azure Portal Wyszukaj i wybierz nazwę wystąpienia usługi Azure kontrolą, które zostało utworzone jako warunek wstępny.
   1. Wybierz sekcję **Przegląd** i Zapisz identyfikator GUID **identyfikatora subskrypcji**.

   > [!NOTE]
   > Upewnij się, że używasz tej samej subskrypcji, w której utworzono konto usługi Azure kontrolą. Jest to ta sama subskrypcja, która została umieszczona na liście dozwolonych.

* Nazwa_katalogu: nazwa konta usługi Azure kontrolą utworzonego w [ramach tworzenia konta usługi Azure kontrolą](create-catalog-portal.md).

* CatalogResourceGroupName: Nazwa grupy zasobów, w której utworzono konto usługi Azure kontrolą.

### <a name="verify-permissions"></a>Weryfikowanie uprawnień

Wykonaj następujące kroki, aby dodać użytkownika uruchamianego przy użyciu skryptu do konta usługi Azure kontrolą, które zostało utworzone jako warunek wstępny. Użytkownicy potrzebują zarówno *kontrolą danych Curator* , jak i *kontrolą ról administratora źródła danych* . 

Jeśli konto usługi Azure kontrolą zostało utworzone samodzielnie, nastąpi automatyczne uzyskanie dostępu i może to pominąć tę sekcję.

1. Przejdź do strony kont kontrolą w Azure Portal i wybierz konto usługi Azure kontrolą, które chcesz zmodyfikować.

1. Na stronie konta wybierz kartę **Kontrola dostępu (IAM)** i **+ Dodaj**.

1. Wybierz pozycję **Dodaj przypisanie roli**.

1. Wprowadź **rolę kontrolą Data Curator** dla *roli*.
 
1. Użyj wartości domyślnej, aby *przypisać dostęp do*. Wartością domyślną powinna być **użytkownik, Grupa lub nazwa główna usługi**.

1. Wprowadź nazwę użytkownika, który uruchomił skrypt w **SELECT**.

1. Wybierz pozycję **Zapisz**.

1. Powtórz poprzednie kroki z ustawioną *rolą* **kontrolą rolę administratora źródła danych**.

Aby uzyskać więcej informacji, zobacz [uprawnienia katalogu](catalog-permissions.md).

### <a name="run-the-client-side-setup-scripts"></a>Uruchamianie skryptów instalacji po stronie klienta

Po zakończeniu konfiguracji wykazu Uruchom następujące skrypty w oknie programu PowerShell, aby utworzyć zasoby, zastępując symbole zastępcze wcześniej zebranymi wartościami.

1. Użyj poniższego polecenia, aby przejść do katalogu Start Kit. Zamień `path-to-starter-kit` na ścieżkę folderu wyodrębnionego pliku.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. Następujące polecenie ustawia zasady wykonywania dla komputera lokalnego. Po wyświetleniu monitu **o zmianę zasad wykonywania wprowadź wartość** dla *opcji tak na wszystko* .

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Połącz się z platformą Azure przy użyciu następującego polecenia. Zamień symbole zastępcze `TenantID` i `SubscriptionID`.

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   Po uruchomieniu polecenia może pojawić się okno podręczne logowania przy użyciu poświadczeń Azure Active Directory.


1. Użyj poniższego polecenia, aby uruchomić zestaw startowy. Zastąp `CatalogName` `TenantID` `SubscriptionID` `NewResourceGroupName` symbole zastępcze,,, i `CatalogResourceGroupName` . W przypadku programu `NewResourceGroupName` należy użyć unikatowej nazwy (tylko z małymi znakami alfanumerycznymi) dla grupy zasobów, która będzie zawierać dane.

   > [!IMPORTANT]
   > **Newresourcegroupname** używa liczb i małych liter i musi być krótszy niż 17 znaków. **Nie są dozwolone wielkie litery i znaki specjalne.** To ograniczenie pochodzi z reguł nazewnictwa kont magazynu.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

Skonfigurowanie środowiska może potrwać do 10 minut. W tym czasie mogą pojawić się różne okna podręczne, które można zignorować. Nie zamykaj okna **BlobDataCreator.exe** ; jest on automatycznie zamykany po zakończeniu.

Gdy zobaczysz komunikat `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` , poczekaj, aż inne wystąpienie **BlobDataCreator.exe** zostanie uruchomione i zakończy działanie.

> [!IMPORTANT]
> W przypadku zaprzestania zmniejszania liczby aktywnych zadań, możesz zamknąć okno twórcy obiektów blob i nacisnąć klawisz ENTER w oknie programu PowerShell.

Po zakończeniu procesu zostanie utworzona grupa zasobów o podanej nazwie. W tej grupie zasobów znajdują się Azure Data Factory, Azure Blob Storage i Azure Data Lake Storage Gen2. Grupa zasobów jest zawarta w określonej subskrypcji.

## <a name="scan-data-into-the-catalog"></a>Skanuj dane do wykazu

Skanowanie to proces, za pomocą którego katalog łączy się bezpośrednio ze źródłem danych w harmonogramie określonym przez użytkownika. Wykaz odzwierciedla dane firmy przez skanowanie, pochodzenie, portal i interfejs API. Cele obejmują badanie elementów wewnątrz, wyodrębnianie schematów i podejmowanie prób zrozumienia semantyki. W tej sekcji skonfigurujesz skanowanie źródeł danych wygenerowanych przy użyciu zestawu startowego.

Uruchomiony skrypt zestawu startowego utworzył dwa źródła danych, Magazyn obiektów blob platformy Azure i Azure Data Lake Storage Gen2. Te źródła danych można skanować do wykazu po jednej naraz.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>Uwierzytelnianie w magazynie przy użyciu tożsamości zarządzanej

Tożsamość zarządzana o tej samej nazwie, co konto usługi Azure kontrolą, jest automatycznie tworzona podczas tworzenia konta. Aby móc skanować dane, musisz nadać uprawnienia roli Azure kontrolą na kontach magazynu.

1. Przejdź do grupy zasobów, która została utworzona przez zestaw startowy, a następnie wybierz konto magazynu obiektów BLOB.

1. Wybierz pozycję **Access Control (IAM)** z menu nawigacji po lewej stronie. Następnie wybierz pozycję **+ Dodaj**.

1. Ustaw rolę na **czytnik danych obiektów blob magazynu** i wprowadź nazwę konta usługi Azure kontrolą do **wybrania**. Następnie wybierz pozycję **Zapisz** , aby nadać temu przypisaniu roli konto kontrolą.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="Dodaj przypisanie roli":::

1. Powtórz poprzednie kroki dla Azure Data Lake Storage Gen2.

### <a name="scan-your-data-sources"></a>Skanuj źródła danych

1. Przejdź do zasobu usługi Azure kontrolą w [Azure Portal](https://portal.azure.com) i wybierz pozycję *Otwórz kontrolą Studio*. Nastąpi automatyczne przekierowanie do strony głównej programu kontrolą Studio.

1. Wybierz pozycję **źródła** na stronie sieci Web wykazu, a następnie wybierz pozycję **zarejestruj**. Następnie wybierz pozycję **Azure Blob Storage** i **Kontynuuj**.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="Zarejestruj zasób magazynu obiektów BLOB":::

1. Na stronie **Rejestruj źródła** wprowadź **nazwę**. Wybierz **nazwę konta magazynu** dla konta usługi Azure Blob Storage utworzonego wcześniej przy użyciu zestawu startowego. Konto ma nazwę `<YourResourceGroupName>adcblob` . Wybierz pozycję **Zakończ**.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Zrzut ekranu przedstawiający ustawienia umożliwiające zarejestrowanie źródła danych usługi Azure Blob Storage." border="true":::

1. W widoku mapy **źródła danych** wybierz pozycję **Nowe skanowanie** na kafelku BLOB Storage platformy Azure.

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania konfiguracji skanowania ze źródła danych." border="true":::

1. Na stronie **skanowanie** wprowadź nazwę skanowania, wybierz swoją tożsamość zarządzaną na liście rozwijanej **poświadczenie** i **Kontynuuj**.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Skanuj magazyn obiektów BLOB w usłudze Azure kontrolą":::

1. Możesz ograniczyć skanowanie do poszczególnych obiektów BLOB. W tym samouczku Zadbaj o sprawdzenie wszystkich zasobów, aby przeskanować wszystko i **kontynuować**.

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="Określanie zakresu skanowania magazynu":::

1. Wybierz domyślny zestaw reguł skanowania i **Kontynuuj**.

1. Wyzwalacz skanowania można skonfigurować do skanowania cyklicznego. Na potrzeby tego samouczka wybierz **jeden raz** i **Kontynuuj**.

1. Wybierz pozycję **Zapisz i uruchom** , aby zakończyć skanowanie.

1. Powtórz poprzednie kroki, aby przeskanować konto Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Uruchom skrypt zestawu startowego, aby skonfigurować środowisko ochrony danych.
> * Skanuj dane do wykazu usługi Azure kontrolą.

Przejdź do następnego samouczka, aby dowiedzieć się, jak nawigować po stronie głównej i wyszukać element zawartości.

> [!div class="nextstepaction"]
> [Nawigowanie po stronie głównej i wyszukiwanie zasobu](tutorial-asset-search.md)

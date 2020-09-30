---
title: Samouczek dotyczący eksportowania danych z Azure Data Box | Microsoft Docs
description: Informacje o wymaganiach wstępnych dotyczących wdrażania i sposobach eksportowania danych z Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 5c3f87620c8a2a2d2438d7a5630541c0f76f9f17
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575575"
---
# <a name="tutorial-create-export-order-for-azure-data-box-preview"></a>Samouczek: Tworzenie kolejności eksportu dla Azure Data Box (wersja zapoznawcza)

Azure Data Box to rozwiązanie hybrydowe, które umożliwia przenoszenie danych z platformy Azure do lokalizacji. W tym samouczku opisano sposób tworzenia zamówienia eksportu dla Azure Data Box. Głównym powodem tworzenia zamówienia eksportu jest odzyskanie awaryjne, w przypadku którego zostanie naruszone bezpieczeństwo magazynu lokalnego i należy przywrócić kopię zapasową.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne dotyczące eksportu
> * Zamówienie urządzenie Data Box do eksportu
> * Śledzenie kolejności eksportu
> * Anuluj kolejność eksportowania

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed zapisaniem urządzenia należy spełnić następujące wymagania wstępne dotyczące konfiguracji usługi urządzenie Data Box i urządzenia.

### <a name="for-service"></a>Na potrzeby usługi

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Upewnij się, że masz istniejącą grupę zasobów, której możesz używać z Azure Data Box.

* Upewnij się, że konto usługi Azure Storage, z którego chcesz wyeksportować dane, jest jednym z obsługiwanych typów kont magazynu, zgodnie z opisem [obsługiwanych kont magazynu dla urządzenie Data Box](data-box-system-requirements.md#supported-storage-accounts).

### <a name="for-device"></a>Na potrzeby urządzenia

Przed rozpoczęciem upewnij się, że:

* Należy posiadać komputer hosta podłączony do sieci centrum danych. Dane zostaną skopiowane z Azure Data Box na ten komputer. Na komputerze hosta musi działać obsługiwany system operacyjny, zgodnie z opisem w [wymaganiach dotyczących systemu Azure Data Box](data-box-system-requirements.md).

* Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10-GbE nie jest dostępne, można użyć linku danych 1 GbE, ale ma to wpływ na szybkość kopiowania.

## <a name="order-data-box-for-export"></a>Zamówienie urządzenie Data Box eksportu

Wykonaj poniższe czynności w witrynie Azure Portal, aby zamówić urządzenie.

1. Użyj swoich poświadczeń platformy Microsoft Azure, aby zalogować się pod następującym adresem URL: [https://portal.azure.com](https://portal.azure.com).

2. Wybierz pozycję **+ Utwórz zasób** i wyszukaj wartość *Azure Data Box*. Wybierz pozycję **Azure Data Box**.

   ![Tworzenie zasobu](media/data-box-deploy-export-ordered/azure-data-box-export-00b.png)

3. Wybierz pozycję **Utwórz**.

   ![Utwórz Azure Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-00c.png)

4. Sprawdź, czy usługa Azure Data Box jest dostępna w Twoim regionie. Wprowadź lub wybierz poniższe informacje, a następnie wybierz pozycję **Zastosuj**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Typ transferu     | Wybierz pozycję **Eksportuj do platformy Azure**.        |
    |Subskrypcja     | Wybierz subskrypcję EA, CSP lub dostęp sponsorowany Azure dla usługi Data Box. <br> Subskrypcja jest połączona z kontem rozliczeniowym.       |
    |Grupa zasobów     |    Wybierz istniejącą grupę zasobów. <br> Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem.         |
    |Źródłowy region platformy Azure    |    Wybierz region platformy Azure, w którym są obecnie używane dane.         |
    |Kraj docelowy     |     Wybierz kraj, w którym chcesz wysłać urządzenie.        |

   ![Wybierz ustawienia urządzenie Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-01.png)

5. Wybierz pozycję **Data Box**. Maksymalna użyteczna pojemność dla pojedynczej kolejności wynosi 80 TB. W przypadku większych ilości danych możesz utworzyć wiele zamówień.

   ![Wybierz pojemność urządzenie Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-02b.png)

6. W **kolejności**Określ **podstawowe** informacje o kolejności. Wprowadź lub wybierz poniższe informacje, a następnie wybierz przycisk **Dalej**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja     | Subskrypcja jest automatycznie wypełniana na podstawie wcześniejszego wyboru.|
    |Grupa zasobów | Wybrana wcześniej Grupa zasobów. |
    |Nazwa zamówienia eksportu     |  Podaj przyjazną nazwę, aby śledzić zamówienie. <br> Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą.      |

    ![Podstawowe informacje o kolejności eksportu](media/data-box-deploy-export-ordered/azure-data-box-export-03.png)

    Wybierz pozycję **Dalej: wybór danych** , aby wykonać operację.

7. W **obszarze Wybór danych**wybierz pozycję **Dodaj konto magazynu i typ eksportu**.

    ![Dodaj konto magazynu i typ eksportu](media/data-box-deploy-export-ordered/azure-data-box-export-03b.png)

8. W obszarze **Wybierz opcję eksportu**Określ szczegóły opcji eksportowania. Wprowadź lub wybierz poniższe informacje, a następnie wybierz pozycję **Dodaj**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Konto magazynu     | Konto usługi Azure Storage, z którego chcesz wyeksportować dane. |
    |Typ eksportu     | Określa typ danych do wyeksportowania ze **wszystkich obiektów** i **Użyj pliku XML**.<ul><li> **Wszystkie obiekty** — określa, że zadanie eksportuje wszystkie dane w zależności od wyboru **opcji transferu**.</li><li> **Użyj pliku XML** — określa plik XML, który zawiera zestaw ścieżek i prefiksów obiektów blob i/lub plików, które mają zostać wyeksportowane z konta magazynu. Plik XML musi znajdować się w kontenerze wybranego konta magazynu, a wybieranie z udziałów plików nie jest obecnie obsługiwane. Plik musi być niepustym plikiem XML.</li></ul>        |
    |Opcje transferu     |  Określa opcje transferu danych z opcji **Zaznacz wszystko**, **wszystkie obiekty blob**i **wszystkie pliki**. <ul><li> **Zaznacz wszystko** — określa, że eksportowane są wszystkie obiekty blob i pliki platformy Azure. Jeśli używasz konta magazynu, które obsługuje tylko obiekty blob (Blob Storage account), opcja **wszystkie pliki** nie zostanie wybrana.</li><li> **Wszystkie obiekty blob** — określa, że eksportowane są tylko obiekty blob blok i Page.</li><li> **Wszystkie pliki** — określa, że wszystkie pliki są eksportowane z wyjątkiem obiektów BLOB. Typ konta magazynu (GPv1 i GPv2, magazyn Premium Storage lub BLOB Storage) określa typy danych, które można wyeksportować. Aby uzyskać więcej informacji, zobacz [obsługiwane konta magazynu do eksportowania](../storage/common/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Dołącz pełny dziennik     | Wskazuje, czy chcesz uzyskać pełny plik dziennika zawierający listę wszystkich plików, które zostały pomyślnie wyeksportowane.        |

    > [!NOTE]
    >
    > Jeśli wybierzesz opcję **Użyj pliku XML** dla ustawienia **Typ eksportu** , musisz upewnić się, że kod XML zawiera prawidłowe ścieżki i/lub prefiksy. Należy utworzyć i podać plik XML.  Jeśli plik jest nieprawidłowy lub żadne dane nie pasują do określonych ścieżek, porządek kończy się częściowymi danymi lub nie są eksportowane żadne dane.

    Aby dowiedzieć się, jak dodać plik XML do kontenera, zobacz [Eksportowanie kolejności przy użyciu pliku XML](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Wybierz opcję eksportu](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

   Aby zobaczyć przykład danych wejściowych XML, zobacz [przykładowe dane wejściowe XML](data-box-deploy-export-ordered.md#sample-xml-file)

9. W **obszarze Wybór danych**przejrzyj ustawienia, a następnie wybierz pozycję **dalej: zabezpieczenia>**.

   ![Dane kontaktowe](media/data-box-deploy-export-ordered/azure-data-box-export-05.png)

1. W obszarze **zabezpieczenia**, jeśli chcesz włączyć podwójne szyfrowanie oparte na oprogramowaniu, wybierz pozycję **Włącz podwójne szyfrowanie dla zamówienia**. 

   Szyfrowanie oparte na oprogramowaniu jest wykonywane poza szyfrowaniem AES-256 bitowym danych na urządzenie Data Box.

   > [!NOTE]
   > Włączenie tej opcji może spowodować wydłużenie przetwarzania zamówień i kopiowania danych. Po utworzeniu zamówienia nie można zmienić tej opcji.

   ![Ekran zabezpieczenia dla importu pola danych, podwójne szyfrowanie](media/data-box-deploy-export-ordered/azure-data-box-export-05b.png)

   Wybierz pozycję **Dalej: szczegóły kontaktu** , aby kontynuować.

10. W obszarze **szczegóły kontaktu**wybierz pozycję **+ Dodaj adres wysyłkowy** , aby wprowadzić informacje o wysyłce.

    ![Dodaj adres wysyłkowy](media/data-box-deploy-export-ordered/azure-data-box-export-06.png)

11. W obszarze **Dodaj adres wysyłkowy**Podaj imię i nazwisko, nazwę i adres pocztowy firmy oraz prawidłowy numer telefonu. Wybierz przycisk **Weryfikuj**. Usługa zweryfikuje adres wysyłkowy pod kątem dostępności usługi. Jeśli ta usługa jest dostępna dla podanego adresu wysyłkowego, otrzymasz odpowiednie powiadomienie.

    ![Zweryfikuj adres wysyłkowy](media/data-box-deploy-export-ordered/azure-data-box-export-07.png)

    Jeśli porządkuje się w regionie, w którym jest dostępna samodzielna wysyłka, możesz wybrać tę opcję. Aby uzyskać więcej informacji na temat samozarządzanej wysyłki, zobacz [Korzystanie z funkcji samozarządzanej wysyłki](data-box-portal-customer-managed-shipping.md).

12. Wybierz pozycję **Dodaj adres wysyłkowy** po pomyślnym sprawdzeniu szczegółów wysyłki.

13. W polu **szczegóły kontaktu**Przejrzyj adres wysyłkowy i adres e-mail. Usługa wysyła powiadomienia e-mail dotyczące wszystkich aktualizacji stanu zamówienia na określone adresy e-mail.

    Zalecamy użycie grupowego adresu e-mail, aby otrzymywać powiadomienia, jeśli administrator opuści grupę.

    ![Szczegóły zamówienia](media/data-box-deploy-export-ordered/azure-data-box-export-09.png)

14. Wybierz kolejno pozycje **Dalej: przegląd i kolejność>**. Musisz zaakceptować warunki i postanowienia, aby kontynuować tworzenie zamówień.

15. Wybierz pozycję **Zamów**. Utworzenie zamówienia trwa kilka minut.

    ![Zatwierdź zamówienie](media/data-box-deploy-export-ordered/azure-data-box-export-10.png)

## <a name="export-order-using-xml-file"></a>Eksportowanie zamówienia przy użyciu pliku XML

W przypadku wybrania opcji **Użyj pliku XML**można określić określone kontenery i obiekty blob (stronę i blok), które mają zostać wyeksportowane. Aby sformatować kod XML, należy postępować zgodnie ze specyfikacją [tabeli plików XML](#sample-xml-file) . Poniższe kroki pokazują, jak używać pliku XML do eksportowania danych:

1. W obszarze **Typ eksportu**wybierz pozycję **Użyj pliku XML**. Jest to plik XML, który określa określone obiekty blob i pliki platformy Azure, które mają zostać wyeksportowane. Aby dodać plik XML, wybierz **pozycję kliknij tutaj, aby wybrać plik XML**.
     ![Zrzut ekranu opcji Wybieranie eksportu z kliknięciem tutaj, aby wybrać opcję plik X M L o nazwie out.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-01.png)

2. Wybierz pozycję **+ kontener** , aby utworzyć kontener.
    ![Zrzut ekranu przedstawiający sekcję Containers.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-02.png)

3. Na karcie **nowy kontener** , która znajduje się po prawej stronie Azure Portal, Dodaj nazwę kontenera. Nazwa musi zawierać małe litery i może zawierać cyfry i myślniki "-". Następnie w polu listy rozwijanej wybierz **poziom dostępu publicznego** . Zalecamy wybranie opcji **prywatny (dostęp nieanonimowy)** , aby uniemożliwić innym osobom dostęp do danych. Aby uzyskać więcej informacji na temat poziomów dostępu do kontenera, zobacz [uprawnienia dostępu do kontenera](../storage/blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

   ![Zrzut ekranu przedstawiający kartę nowy kontener z wybraną opcją prywatny (brak dostępu anonimowego).](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-04.png)

4. Wybierz pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający kartę nowy kontener z opcją tworzenia wywołana.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-07.png)

   Jeśli kontener zostanie utworzony pomyślnie, zostanie wyświetlony następujący komunikat:

   ![Zrzut ekranu przedstawiający komunikat "pomyślnie utworzono kontener magazynu".](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-09.png)

5. Wybierz utworzony kontener i kliknij go dwukrotnie.

   ![Zrzut ekranu przedstawiający sekcję kontenerów z kontenerem kontenera my test Private.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-08.png)

6. Dwukrotne kliknięcie kontenera spowoduje wyświetlenie widoku właściwości kontenera. Teraz chcesz dołączyć (lub przeszukać) plik XML zawierający listę obiektów blob i/lub plików platformy Azure, które mają zostać wyeksportowane. Wybierz pozycję **Przekaż**.

   ![Zrzut ekranu okna dialogowego przekazywanie obiektu BLOB z opcją przekazywania o nazwie out.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-10c.png)

7. Plik XML został pomyślnie dodany do kontenera. Eksportowane są tylko obiekty blob i pliki platformy Azure określone w tym formacie XML.

   ![Zrzut ekranu kreatora zamówień z opcją Next: Security o nazwie out.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-12.png)

## <a name="track-the-order"></a>Śledzenie zamówienia

Po złożeniu zamówienia możesz śledzić jego stan w witrynie Azure Portal. Przejdź do zamówienia urządzenia Data Box, a następnie przejdź do obszaru **Omówienie**, aby sprawdzić stan. Zamówienie wyświetlone w portalu ma stan **Zamówione**.

Po zakończeniu przygotowywania urządzenia kopia danych rozpocznie się z wybranych kont magazynu. W portalu jest wyświetlana kolejność **kopiowania danych w toku** .

![urządzenie Data Box przetworzonej kolejności eksportu](media/data-box-deploy-export-ordered/azure-data-box-export-15b.png)

Urządzenie Data Box kopiuje dane z źródłowych kont magazynu. Po zakończeniu kopiowania danych urządzenie Data Box jest zablokowany, a w portalu będzie wyświetlana kolejność w stanie **Zakończono** .

![Ukończono urządzenie Data Box eksportu danych](media/data-box-deploy-export-ordered/azure-data-box-export-15c.png)

Jeśli urządzenie nie jest dostępne, otrzymasz powiadomienie. Jeśli urządzenie jest dostępne, firma Microsoft zidentyfikuje urządzenie do wysłania i przygotuje wysyłkę. Podczas przygotowywania urządzenia są wykonywane następujące akcje:

* Dla każdego konta magazynu skojarzonego z urządzeniem tworzone są udziały SMB.
* Dla każdego udziału generowane są poświadczenia dostępu, takie jak nazwa użytkownika i hasło.
* Urządzenie jest zablokowane i można uzyskać do niego dostęp tylko przy użyciu hasła odblokowywania urządzenia. Aby pobrać hasło, należy zalogować się do konta Azure Portal i wybrać pozycję **szczegóły urządzenia**.

Następnie firma Microsoft przygotuje i przygotowuje urządzenie przez przewoźnika regionalnego. Po wysłaniu dysków otrzymasz numer służący do jego śledzenia. W portalu zamówienie zostanie wysłane ze stanem **Wysłane**.

![urządzenie Data Box zamówienie eksportu zostało wysłane](media/data-box-deploy-export-ordered/azure-data-box-export-16.png)

Jeśli wybrano opcję wysyłki samozarządzanej, otrzymasz powiadomienie e-mail z kolejnymi krokami, gdy urządzenie będzie gotowe do pobrania z centrum danych. Aby uzyskać więcej informacji dotyczących funkcji wysyłki samozarządzanej, zobacz [samozarządzane dostarczanie](data-box-portal-customer-managed-shipping.md).

![Samozarządzane dostarczanie gotowe do pobrania](media/data-box-deploy-export-ordered/azure-data-box-export-17.png)

## <a name="cancel-the-order"></a>Anulowanie zamówienia

Aby anulować tę kolejność, w Azure Portal przejdź do **omówienia** i wybierz pozycję **Anuluj** na pasku poleceń.

Po umieszczeniu zamówienia można je anulować w dowolnym momencie przed rozpoczęciem przetwarzania zamówienia.

Aby usunąć anulowaną kolejność, przejdź do **omówienia** i wybierz pozycję **Usuń** z paska poleceń.

## <a name="sample-xml-file"></a>Przykładowy plik XML

Poniższy kod XML przedstawia przykład nazw obiektów blob, prefiksów obiektów blob i plików platformy Azure zawartych w formacie XML, który jest używany przez zamówienie eksportu po wybraniu opcji **Użyj pliku XML** :

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Niektóre ważne punkty w odniesieniu do plików XML:

* Znaczniki XML są rozróżniane wielkości liter i muszą dokładnie pasować do określonych w powyższym przykładzie.
* Otwieranie i zamykanie tagów musi być zgodne.
* Nieprawidłowe Tagi lub formatowanie XML mogą prowadzić do błędu eksportowania danych.
* Nie zostaną wyeksportowane żadne dane, jeśli obiekt BLOB i/lub prefiks pliku są nieprawidłowe.

### <a name="examples-of-valid-blob-paths"></a>Przykłady prawidłowych ścieżek obiektów BLOB

W poniższej tabeli przedstawiono przykłady prawidłowych ścieżek obiektów blob:

   | Selektor | Ścieżka obiektu BLOB | Opis |
   | --- | --- | --- |
   | Rozpoczyna się od |/ |Eksportuje wszystkie obiekty blob na koncie magazynu |
   | Rozpoczyna się od |/$root/ |Eksportuje wszystkie obiekty blob w kontenerze głównym |
   | Rozpoczyna się od |/containers |Eksportuje wszystkie obiekty blob w kontenerze rozpoczynającym się od **kontenerów** prefiksów |
   | Rozpoczyna się od |/container-name/ |Eksportuje wszystkie obiekty blob w **kontenerze kontenera — nazwa** |
   | Rozpoczyna się od |/container-name/prefix |Eksportuje wszystkie obiekty blob w **kontenerze kontenera — nazwa** rozpoczynająca się **od prefiksu prefiksu** |
   | Jest równe |$root/logo.bmp |Eksportuje **logo.bmp** obiektów BLOB w kontenerze głównym |
   | Jest równe |8tbpageblob/mydata.txt |Eksportuje **mydata.txt** obiektów BLOB w kontenerze **8tbpageblob** |

## <a name="sample-log-files"></a>Przykładowe pliki dziennika

Ta sekcja zawiera przykładowe pliki dziennika, które są generowane podczas eksportowania. Dzienniki błędów są generowane automatycznie. Aby wygenerować pełny plik dziennika, należy wybrać opcję **Dołącz pełny dziennik** w Azure Portal podczas konfigurowania kolejności eksportu.
Aby uzyskać więcej informacji na temat kopiowania i dzienników pełnych, zobacz [kopiowanie dzienników](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
>
> * Wymagania wstępne dotyczące eksportu
> * Zamówienie urządzenie Data Box do eksportu
> * Śledzenie kolejności eksportu
> * Anuluj kolejność eksportowania

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować usługę Data Box.

> [!div class="nextstepaction"]
> [Konfigurowanie usługi Azure Data Box](./data-box-deploy-set-up.md)

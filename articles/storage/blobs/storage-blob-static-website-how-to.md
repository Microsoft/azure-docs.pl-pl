---
title: Hostowanie statycznej witryny sieci Web w usłudze Azure Storage
description: Dowiedz się, jak udostępniać zawartość statyczną (pliki HTML, CSS, JavaScript i Image) bezpośrednio z kontenera na koncie usługi Azure Storage GPv2.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 052a28dc69bf5c758133ca98366efc63105f4a56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289875"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hostowanie statycznej witryny sieci Web w usłudze Azure Storage

Zawartości statycznej (HTML, CSS, JavaScript i plików obrazów) można obsłużyć bezpośrednio w kontenerze na koncie usługi Azure Storage GPv2. Aby dowiedzieć się więcej, zobacz [statyczne hostowanie witryn sieci Web w usłudze Azure Storage](storage-blob-static-website.md).

W tym artykule opisano sposób włączania obsługi statycznej witryny sieci Web przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

> [!NOTE]
> Upewnij się, że utworzono standardowe konto magazynu ogólnego przeznaczenia w wersji 2. Statyczne witryny sieci Web nie są dostępne na żadnym innym typie konta magazynu.

## <a name="enable-static-website-hosting"></a>Włączanie hostingu statycznej witryny sieci Web

Hostowanie statycznej witryny sieci Web to funkcja, którą należy włączyć na koncie magazynu.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/), aby rozpocząć pracę.

2. Znajdź konto magazynu i wyświetl omówienie konta.

3. Wybierz pozycję **Statyczna witryna internetowa**, aby wyświetlić stronę konfiguracji dla statycznych witryn internetowych.

4. Wybierz pozycję **Włączone**, aby umożliwić hostowanie statycznej witryny internetowej na koncie magazynu.

5. W polu **Nazwa dokumentu indeksu** określ domyślną stronę indeksu (na przykład: *index.html*). 

   Gdy użytkownik przejdzie do głównego elementu statycznej witryny internetowej, wyświetlona zostanie domyślna strona indeksu.  

6. W polu **ścieżka dokumentu błędu** określ domyślną stronę błędów (na przykład: *404.html*). 

   Gdy użytkownik spróbuje przejść do strony, która nie istnieje w statycznej witrynie internetowej, wyświetlona zostanie domyślna strona błędu.

7. Kliknij przycisk **Zapisz**. W witrynie Azure Portal jest teraz wyświetlany punkt końcowy statycznej witryny internetowej. 

    ![Włączanie hostowania statycznej witryny internetowej na koncie magazynu](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

<a id="cli"></a>

Można włączyć obsługę statycznej witryny sieci Web przy użyciu [interfejsu Azure Command-Line Interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Najpierw Otwórz [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)lub jeśli interfejs wiersza polecenia platformy Azure został [zainstalowany](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) lokalnie, Otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

3. Włącz hosting statycznej witryny sieci Web.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

   * Zastąp `<error-document-name>` symbol zastępczy nazwą dokumentu błędu, który będzie widoczny dla użytkowników, gdy przeglądarka zażąda strony, która nie istnieje.

   * Zastąp `<index-document-name>` symbol zastępczy nazwą dokumentu indeksu. Ten dokument jest często "index.html".

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Można włączyć hosting statycznej witryny sieci Web przy użyciu modułu Azure PowerShell.

1. Otwórz okno poleceń programu Windows PowerShell.

2. Sprawdź, czy masz Azure PowerShell module AZ w wersji 0,7 lub nowszej.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

3. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

4. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

5. Pobierz kontekst konta magazynu, który definiuje konto magazynu, którego chcesz użyć.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów.

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

6. Włącz hosting statycznej witryny sieci Web.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Zastąp `<error-document-name>` symbol zastępczy nazwą dokumentu błędu, który będzie widoczny dla użytkowników, gdy przeglądarka zażąda strony, która nie istnieje.

   * Zastąp `<index-document-name>` symbol zastępczy nazwą dokumentu indeksu. Ten dokument jest często "index.html".

---

## <a name="upload-files"></a>Przekazywanie plików 

### <a name="portal"></a>[Portal](#tab/azure-portal)

W tych instrukcjach pokazano, jak przekazywać pliki przy użyciu wersji Eksplorator usługi Storage, która pojawia się w Azure Portal. Można jednak również użyć wersji [Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) , która działa poza programem Azure Portal. Można użyć [AzCopy](../common/storage-use-azcopy-v10.md), programu PowerShell, interfejsu wiersza polecenia lub dowolnej aplikacji niestandardowej, która może przekazywać pliki do kontenera **$Web** Twojego konta. Aby zapoznać się z samouczkiem krok po kroku, który przekazuje pliki przy użyciu programu Visual Studio Code, zobacz [Samouczek: hostuje statyczną witrynę sieci Web na BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Wybierz pozycję **Eksplorator usługi Storage (wersja zapoznawcza)**.

2. Rozwiń węzeł **kontenery obiektów BLOB** , a następnie wybierz kontener **$Web** .

3. Wybierz przycisk **Przekaż** , aby przekazać pliki.

   ![Przekazywanie plików](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Jeśli zamierzasz wyświetlić zawartość pliku w przeglądarce, upewnij się, że typ zawartości tego pliku jest ustawiony na `text/html` . 

   ![Sprawdź typy zawartości](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Eksplorator usługi Storage automatycznie ustawia tę właściwość na `text/html` dla powszechnie rozpoznanych rozszerzeń, takich jak `.html` . Jednak w niektórych przypadkach trzeba będzie ustawić to samodzielnie. Jeśli nie ustawisz tej właściwości na `text/html` , przeglądarka wyświetli monit o pobranie pliku zamiast renderowania zawartości. Aby ustawić tę właściwość, kliknij plik prawym przyciskiem myszy, a następnie kliknij polecenie **Właściwości**.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Przekaż obiekty do kontenera *$Web* z katalogu źródłowego.

W tym przykładzie przyjęto założenie, że używasz poleceń z sesji Azure Cloud Shell.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> Jeśli przeglądarka poprosi użytkowników o pobranie pliku zamiast renderowania zawartości, można dołączyć `--content-type 'text/html; charset=utf-8'` do polecenia. 

* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<source-path>` symbol zastępczy ścieżką do lokalizacji plików, które chcesz przekazać.

> [!NOTE]
> Jeśli używasz instalacji lokalizacji interfejsu wiersza polecenia platformy Azure, możesz użyć ścieżki do dowolnej lokalizacji na komputerze lokalnym (na przykład: `C:\myFolder` .
>
> Jeśli używasz Azure Cloud Shell, musisz odwołać się do udziału plików, który jest widoczny dla Cloud Shell. Ta lokalizacja może być udziałem plików w udziale w chmurze lub istniejącym udziałem plików, który można zainstalować z Cloud Shell. Aby dowiedzieć się, jak to zrobić, zobacz [utrwalanie plików w Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Przekaż obiekty do kontenera *$Web* z katalogu źródłowego.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Jeśli przeglądarka poprosi użytkowników o pobranie pliku zamiast renderowania zawartości, można dołączyć `-Properties @{ ContentType = "text/html; charset=utf-8";}` do polecenia.

* Zastąp `<path-to-file>` wartość symbolu zastępczego w pełni kwalifikowaną ścieżką do pliku, który chcesz przekazać (na przykład: `C:\temp\index.html` ).

* Zastąp `<blob-name>` wartość symbolu zastępczego nazwą, która ma dać otrzymany obiekt BLOB (na przykład: `index.html` ).

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>Znajdź adres URL witryny sieci Web

Możesz wyświetlić strony witryny z przeglądarki, używając publicznego adresu URL witryny sieci Web.

### <a name="portal"></a>[Portal](#tab/azure-portal)

W okienku, które pojawia się obok strony Przegląd konta na koncie magazynu, wybierz pozycję **statyczna witryna sieci Web**. Adres URL witryny zostanie wyświetlony w polu **podstawowy punkt końcowy** .

![Metryka statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Znajdź publiczny adres URL statycznej witryny sieci Web za pomocą następującego polecenia:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Znajdź publiczny adres URL statycznej witryny sieci Web za pomocą następującego polecenia:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów.

* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>Włącz metryki na stronach statycznej witryny internetowej

Po włączeniu metryk statystyki ruchu dla plików w kontenerze **$Web** są raportowane na pulpicie nawigacyjnym metryk.

1. Kliknij pozycję **metryki** w sekcji **monitorowanie** w menu konto magazynu.

   > [!div class="mx-imgBorder"]
   > ![Link metryk](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Dane metryk są generowane przez Podłączanie do różnych interfejsów API metryk. W portalu są wyświetlane tylko elementy API używane w danym przedziale czasowym, aby skoncentrować się tylko na elementach członkowskich, które zwracają dane. Aby upewnić się, że można wybrać wymagany element członkowski interfejsu API, pierwszym krokiem jest rozwinięcie przedziału czasu.

2. Kliknij przycisk ramy czasowe, wybierz przedział czasu, a następnie kliknij przycisk **Zastosuj**.

   ![Zakres czasu metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Wybierz pozycję **obiekt BLOB** z listy rozwijanej *przestrzeń nazw* .

   ![Przestrzeń nazw metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Następnie wybierz metrykę **ruchu** wychodzącego.

   ![Metryka statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Wybierz pozycję **sum** z selektora *agregacji* .

   ![Agregacja metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Kliknij przycisk **Dodaj filtr** i wybierz pozycję **nazwa interfejsu API** z selektora *Właściwości* .

   ![Nazwa interfejsu API metryk statycznych witryn sieci Web usługi Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Zaznacz pole wyboru obok pozycji **GetWebContent** w selektorze *wartości* , aby wypełnić raport metryk.

   ![Metryki statycznych witryn sieci Web usługi Azure Storage GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > Pole wyboru **GetWebContent** pojawia się tylko wtedy, gdy ten element członkowski interfejsu API został użyty w danym przedziale czasu. W portalu są wyświetlane tylko elementy API używane w danym przedziale czasowym, aby skoncentrować się tylko na elementach członkowskich, które zwracają dane. Jeśli nie możesz znaleźć konkretnego elementu członkowskiego interfejsu API na tej liście, rozwiń przedział czasu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak skonfigurować domenę niestandardową przy użyciu statycznej witryny sieci Web. Zobacz sekcję [Mapowanie domeny niestandardowej na punkt końcowy usługi Azure Blob Storage](storage-custom-domain-name.md).


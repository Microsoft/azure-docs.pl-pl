---
title: 'Szybki start: usługa Microsoft Azure Data Box Disk | Microsoft Docs'
description: Skorzystaj z tego przewodnika Szybki start, aby przeprowadzić szybkie wdrożenie usługi Azure Data Box Disk w witrynie Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 23615daf4a07e02b01bbd5a9cdf57ec9a81a2b76
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347409"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Szybki start: wdrażanie usługi Azure Data Box Disk za pomocą witryny Azure Portal

W tym przewodniku Szybki start opisano sposób wdrażania usługi Azure Data Box Disk przy użyciu witryny Azure Portal. Opisano czynności szybkiego tworzenia zamówienia, odbioru dysków, ich rozpakowywania i podłączania oraz kopiowania danych na dyski w celu przekazania na platformę Azure.

Aby zyskać szczegółowe instrukcje dotyczące wdrażania i śledzenia krok po kroku, zobacz [Samouczek: zamawianie urządzenia Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true).

::: zone-end

::: zone target="chromeless"

Ten przewodnik zawiera szczegółowe instrukcje dotyczące korzystania z usługi Azure Data Box Disk w witrynie Azure Portal. W tym przewodniku można znaleźć odpowiedzi na poniższe pytania.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Upewnij się, że w ramach Twojej subskrypcji włączono usługę Azure Data Box. Aby włączyć tę usługę w subskrypcji, [zarejestruj się](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Przejrzyj wymagania wstępne** : Sprawdź liczbę dysków i kabli, system operacyjny i inne oprogramowanie.
> - **Podłącz i odblokuj** : Podłącz urządzenie i odblokuj dysk, aby skopiować dane.
> - **Skopiuj dane na dysk i sprawdź ich poprawność** : Skopiuj dane na dyski do wstępnie utworzonych folderów.
> - **Zwróć dyski** : Zwróć dyski do centrum danych platformy Azure, w którym dane zostaną przekazane do Twojego konta magazynu.
> - **Sprawdź dane na platformie Azure** : Przed usunięciem danych z serwera źródłowego sprawdź, czy dane zostały przekazane na Twoje konto magazynu.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Zamówienie

### <a name="portal"></a>[Portal](#tab/azure-portal)

Ten krok zajmuje około 5 minut.

1. Utwórz nowy zasób usługi Azure Data Box w witrynie Azure Portal. 
2. Wybierz subskrypcję, w której włączono tę usługę, a następnie wybierz typ transferu **Import**. Podaj **Kraj źródłowy** , w którym są przechowywane dane, oraz **Docelowy region platformy Azure** , do którego mają zostać przekazane dane.
3. Wybierz pozycję **Data Box Disk**. Maksymalna pojemność tego rozwiązania to 35 TB. W przypadku większych ilości danych możesz utworzyć większą liczbę zamówień.  
4. Wprowadź szczegóły zamówienia i informacje dotyczące wysyłki. Jeśli ta usługa jest dostępna w Twoim regionie, podaj adresy e-mail na potrzeby powiadomień, zapoznaj się z podsumowaniem i utwórz zamówienie.

Po utworzeniu zamówienia dyski zostaną przygotowane do wysłania.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Skorzystaj z następujących poleceń interfejsu wiersza polecenia platformy Azure, aby utworzyć zadanie rozwiązania Data Box Disk.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Uruchom polecenie [az group create](/cli/azure/group#az_group_create), aby utworzyć grupę zasobów lub użyć istniejącej grupy zasobów:

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. Użyj polecenia [az storage account create](/cli/azure/storage/account#az_storage_account_create), aby utworzyć konto magazynu lub użyć istniejącego konta magazynu:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Uruchom polecenie [az databox job create](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_create), aby utworzyć zadanie urządzenia Data Box z jednostką SKU o wartości DataBoxDisk:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. Uruchom polecenie [az databox job update](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_update), aby zaktualizować zadanie. W tym przykładzie zmieniono nazwę i adres e-mail kontaktu:

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   Uruchom polecenie [az databox job show](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_show), aby uzyskać informacje o zadaniu:

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   Użyj polecenia [az databox job list]( /cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_list), aby wyświetlić wszystkie zadania urządzenia Data Box dla grupy zasobów:

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   Uruchom polecenie [az databox job cancel](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_cancel), aby anulować zadanie:

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   Uruchom polecenie [az databox job delete](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_delete), aby usunąć zadanie:

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. Użyj polecenia [az databox job list-credentials]( /cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_list_credentials), aby wyświetlić listę poświadczeń dla zadania urządzenia Data Box:

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

Po utworzeniu zamówienia urządzenie zostanie przygotowane do wysłania.

---

## <a name="unpack"></a>Rozpakowywanie

Ten krok zajmuje około 5 minut.

Urządzenia Data Box Disk są wysyłane w opakowaniu UPS Express Box. Otwórz opakowanie i sprawdź, czy zawiera:

- od 1 do 5 dysków USB zapakowanych w folię bąbelkową,
- jeden kabel połączeniowy dla każdego dysku,
- etykietę wysyłkową na potrzeby wysyłki zwrotnej.

## <a name="connect-and-unlock"></a>Podłączanie i odblokowywanie

Ten krok zajmuje około 5 minut.

1. Podłącz dysk do komputera z obsługiwaną wersją systemu Windows/Linux za pomocą dołączonego kabla. Aby uzyskać więcej informacji na temat obsługiwanych wersji systemu operacyjnego, zapoznaj się z [wymaganiami systemowymi rozwiązania Azure Data Box Disk](data-box-disk-system-requirements.md). 
2. Aby odblokować dysk:

    1. W witrynie Azure Portal przejdź do pozycji **Ogólne > Szczegóły urządzenia** i uzyskaj klucz dostępu.
    2. Pobierz i wyodrębnij przeznaczone dla danego systemu operacyjnego narzędzie do odblokowywania urządzenia Data Box Disk na komputerze, którego używasz do skopiowania danych na dyski. 
    3. Uruchom narzędzie do odblokowywania urządzenia Data Box Disk i podaj klucz dostępu. W przypadku ponownego użycia dysku uruchom ponownie narzędzie do odblokowywania i podaj klucz dostępu. **Do odblokowania dysku nie należy używać okna dialogowego funkcji BitLocker ani klucza funkcji BitLocker.** Aby uzyskać więcej informacji na temat sposobu odblokowywania dysków, zobacz [Odblokowywanie dysków na komputerze klienckim z systemem Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) lub [Odblokowywanie dysków na komputerze klienckim z systemem Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. W narzędziu jest wyświetlana litera dysku przypisana do danego dysku. Zanotuj tę literę dysku. Będzie używana w kolejnych krokach.

## <a name="copy-data-and-validate"></a>Kopiowanie danych i weryfikacja

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Na dysku znajdują się następujące foldery: *PageBlob* , *BlockBlob* , *AzureFile* , *ManagedDisk* i *DataBoxDiskImport*. Przeciągnij i upuść dane, które mają zostać zaimportowane jako blokowe obiekty blob, do folderu *BlockBlob*. Analogicznie przeciągnij i upuść dane w formatach takich jak VHD/VHDX do folderu *PageBlob* oraz odpowiednie dane do folderu *AzureFile*. Skopiuj wirtualne dyski twarde, które chcesz przekazać jako dyski zarządzane, do folderu w folderze *ManagedDisk*.

    Dla każdego podfolderu w folderach *BlockBlob* i *PageBlob* zostanie utworzony kontener na koncie usługi Azure Storage. Dla podfolderu w folderze *AzureFile* jest tworzony udział plików.

    Wszystkie pliki w folderach *BlockBlob* i *PageBlob* zostaną skopiowane do domyślnego kontenera `$root` na koncie usługi Azure Storage. Skopiuj pliki do folderu w folderze *AzureFile*. Przekazanie wszelkich plików skopiowanych bezpośrednio do folderu *AzureFile* zakończy się niepowodzeniem i zostaną one przekazane jako blokowe obiekty blob.

    > [!NOTE]
    > - Wszystkie kontenery, obiekty blob i pliki powinny mieć nazwy zgodne z [konwencją nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). W przypadku niezgodności z tymi regułami przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
    > - Upewnij się, że rozmiar żadnego z kopiowanych plików nie przekracza ~4,75 TiB w przypadku blokowych obiektów blob, ~8 TiB w przypadku stronicowych obiektów blob i ~1 TiB w przypadku plików usługi Azure Files.

2. **(Opcjonalne, ale zalecane)** Po zakończeniu kopiowania zdecydowanie zaleca się uruchomienie pliku `DataBoxDiskValidation.cmd` znajdującego się w folderze *DataBoxDiskImport* i wybranie opcji 1 w celu zweryfikowania plików. Jeśli pozwoli na to czas, zaleca się również użycie opcji 2 w celu wygenerowania sum kontrolnych na potrzeby weryfikacji (ta operacja może zająć więcej czasu w zależności od rozmiaru danych). Te kroki minimalizują ryzyko wystąpienia błędów podczas przekazywania danych na platformę Azure.
3. Bezpiecznie usuń dysk.

## <a name="ship-to-azure"></a>Wysyłka do platformy Azure

Ten krok zajmuje około 5–7 minut.

1. Umieść wszystkie dyski w oryginalnym opakowaniu. Użyj dołączonej etykiety wysyłkowej. W przypadku uszkodzenia lub utraty etykiety pobierz etykietę z portalu. W sekcji **Przegląd** kliknij pozycję **Pobierz etykietę wysyłkową** na pasku poleceń.
2. Oddaj zapieczętowaną paczkę firmie przewozowej.  

Usługa Data Box Disk wysyła powiadomienie e-mail i aktualizuje stan zamówienia w witrynie Azure Portal.

## <a name="verify-your-data"></a>Weryfikowanie danych

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Gdy urządzenie Data Box Disk zostanie podłączone do sieci w centrum danych platformy Azure, automatycznie rozpocznie się przekazywanie danych na platformę Azure.
2. Usługa Azure Data Box powiadomi Cię o zakończeniu kopiowania danych w witrynie Azure Portal.
    
    1. Sprawdź dzienniki błędów pod kątem ewentualnych niepowodzeń i podejmij odpowiednie działania.
    2. Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten krok zajmuje około 2–3 minut.

Aby wyczyścić zasoby, możesz anulować zamówienie urządzenia Data Box i usunąć je.

- Możesz anulować zamówienie urządzenia Data Box w witrynie Azure Portal przed jego przetworzeniem. Po przetworzeniu zamówienia nie można go anulować. Realizacja zamówienia będzie kontynuowana aż do etapu ukończenia.

    Aby anulować zamówienie, w sekcji **Przegląd** kliknij pozycję **Anuluj** na pasku poleceń.  

- Możesz usunąć zamówienie, gdy jego stan w witrynie Azure Portal to **Ukończono** lub **Anulowano**.

    Aby usunąć zamówienie, w sekcji **Przegląd** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym przewodnikiem Szybki start wdrożono usługę Azure Data Box Disk w celu zaimportowania danych na platformę Azure. Aby dowiedzieć się więcej na temat zarządzania usługą Azure Data Box Disk, przejdź do kolejnego samouczka:

> [!div class="nextstepaction"]
> [Use the Azure portal to administer Data Box Disk (Administrowanie usługą Data Box Disk w witrynie Azure Portal)](data-box-portal-ui-admin.md)

::: zone-end

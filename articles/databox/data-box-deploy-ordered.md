---
title: Samouczek przedstawiający kolejność Azure Data Box | Microsoft Docs
description: Informacje o wymaganiach wstępnych dotyczących wdrażania oraz sposobie zamawiania urządzenia Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392487"
---
# <a name="tutorial-order-azure-data-box"></a>Samouczek: Zamawianie urządzenia Azure Data Box

Azure Data Box to hybrydowe rozwiązanie, które umożliwia importowanie danych lokalnych na platformę Azure szybko, łatwo i bez problemów. Przesyłasz swoje dane na urządzenie magazynujące dostarczane przez firmę Microsoft, którego pojemność do wykorzystania to 80 TB, a następnie odsyłasz urządzenie z powrotem do firmy Microsoft. Te dane są następnie przekazywane na platformę Azure.

W tym samouczku opisano sposób zamawiania usługi Azure Data Box. Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne dotyczące wdrażania usługi Data Box
> * Zamawianie usługi Data Box
> * Śledzenie zamówienia
> * Anulowanie zamówienia

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/portal)

Przed wdrożeniem urządzenia wykonaj następujące wymagania wstępne dotyczące konfiguracji usługi urządzenie Data Box Service i Device:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

Możesz zalogować się do platformy Azure i uruchamiać polecenia interfejsu wiersza poleceń platformy Azure na jeden z dwóch sposobów:

* Interfejs wiersza polecenia można zainstalować i uruchomić polecenie CLI lokalnie.
* Poleceń interfejsu wiersza polecenia można uruchomić z poziomu Azure Portal w Azure Cloud Shell.

Użyjemy interfejsu wiersza polecenia platformy Azure za pomocą programu Windows PowerShell dla samouczka, ale możesz wybrać jedną z opcji.

### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

* Zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w wersji 2.0.67 lub nowszej. Alternatywnie możesz zainstalować plik [MSI](https://aka.ms/installazurecliwindows).

#### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz okno polecenia programu Windows PowerShell i zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) :

```azurecli
PS C:\Windows> az login
```

Oto dane wyjściowe z pomyślnego logowania:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

#### <a name="install-the-azure-data-box-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia Azure Data Box

Aby można było użyć poleceń interfejsu wiersza polecenia Azure Data Box, należy zainstalować rozszerzenie. Rozszerzenia interfejsu wiersza polecenia platformy Azure umożliwiają dostęp do poleceń eksperymentalnych i w wersji wstępnej, które nie zostały jeszcze wysłane jako część podstawowego interfejsu wiersza polecenia. Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [Korzystanie z rozszerzeń przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).

Aby zainstalować rozszerzenie dla Azure Data Box, uruchom następujące `az extension add --name databox` polecenie:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Jeśli rozszerzenie zostało pomyślnie zainstalowane, zobaczysz następujące dane wyjściowe:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

### <a name="use-azure-cloud-shell"></a>Używanie usługi Azure Cloud Shell

Możesz użyć [Azure Cloud Shell](https://shell.azure.com/), hostowanego środowiska powłoki interaktywnej platformy Azure, za pomocą przeglądarki, aby uruchomić polecenia CLI. Azure Cloud Shell obsługuje bash lub Windows PowerShell z usługami platformy Azure. Interfejs wiersza polecenia platformy Azure jest wstępnie zainstalowany i skonfigurowany do użycia z Twoim kontem. Kliknij przycisk Cloud Shell w menu w prawym górnym rogu Azure Portal:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Przycisk uruchamia interaktywną powłokę, której można użyć, aby wykonać kroki opisane w tym artykule jak to zrobić.

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>Zamawianie urządzenia Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Aby zamówić urządzenie, wykonaj następujące czynności w Azure Portal.

1. Użyj swoich poświadczeń platformy Microsoft Azure, aby zalogować się pod następującym adresem URL: [https://portal.azure.com](https://portal.azure.com).
2. Kliknij pozycję **+ Utwórz zasób** i wyszukaj wartość *Azure Data Box*. Kliknij pozycję **Azure Data Box**.

   [![Wyszukiwanie pozycji Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Kliknij pozycję **Utwórz**.

4. Sprawdź, czy usługa Data Box jest dostępna w Twoim regionie. Wprowadź lub wybierz poniższe informacje, a następnie wybierz pozycję **Zastosuj**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja     | Wybierz subskrypcję EA, CSP lub dostęp sponsorowany Azure dla usługi Data Box. <br> Subskrypcja jest połączona z kontem rozliczeniowym.       |
    |Typ transferu     | Wybierz pozycję **Importuj na platformę Azure**.        |
    |Kraj/region źródłowy    |    Wybierz kraj/region, w którym aktualnie znajdują się dane.         |
    |Docelowy region platformy Azure     |     Wybierz region platformy Azure, do którego chcesz przenieść dane.        |

5. Wybierz pozycję **Data Box**. Maksymalna użyteczna pojemność dla pojedynczej kolejności wynosi 80 TB. W przypadku większych ilości danych możesz utworzyć wiele zamówień.

      [![Wybierz opcję urządzenie Data Box 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. W obszarze **Zamówienie** określ **szczegóły zamówienia**. Wprowadź lub wybierz poniższe informacje, a następnie wybierz przycisk **Dalej**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Nazwa     |  Podaj przyjazną nazwę, aby śledzić zamówienie. <br> Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą.      |
    |Grupa zasobów     |    Użyj istniejącej grupy lub utwórz nową. <br> Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem.         |
    |Docelowy region platformy Azure     | Wybierz region swojego konta magazynu. <br> Aby uzyskać więcej informacji, przejdź do sekcji [Dostępność regionalna](data-box-overview.md#region-availability).        |
    |Miejsce docelowe magazynu     | Wybierz konto magazynu, dyski zarządzane lub obie te opcje. <br> W oparciu o wybrany region platformy Azure wybierz z listy filtrowanej istniejących kont magazynu co najmniej jedno z nich. Urządzenie Data Box można połączyć z maksymalnie 10 kontami magazynu. <br> Można również utworzyć nowe **konto ogólnego przeznaczenia w wersji 1**, **konto ogólnego przeznaczenia w wersji 2** lub **konto usługi Blob Storage**. <br>Konta magazynu z sieciami wirtualnymi są obsługiwane. Aby umożliwić usłudze Data Box współpracę z zabezpieczonymi kontami magazynu, włącz usługi zaufane w ustawieniach zapory sieciowej dla konta magazynu. Aby uzyskać więcej informacji, zobacz jak [dodać Azure Data Box jako zaufaną usługę](../storage/common/storage-network-security.md#exceptions).|

    Jeśli korzystasz z konta magazynu jako miejsca docelowego magazynu, zobaczysz następujący ekran:

    ![Zamówienie urządzenie Data Box dla konta magazynu](media/data-box-deploy-ordered/order-storage-account.png)

    Jeśli przy użyciu urządzenie Data Box utworzyć dyski zarządzane na podstawie lokalnych wirtualnych dysków twardych (VHD), należy również podać następujące informacje:

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Grupy zasobów     | Utwórz nowe grupy zasobów, jeśli zamierzasz tworzyć dyski zarządzane na podstawie lokalnych dysków VHD. Istniejącej grupy zasobów można użyć tylko wtedy, gdy grupa zasobów została utworzona wcześniej podczas tworzenia kolejności urządzenie Data Box dla dysku zarządzanego przez usługę urządzenie Data Box. <br> Nazwy poszczególnych grup zasobów rozdziel średnikami. Obsługiwanych jest maksymalnie 10 grup zasobów.|

    ![Kolejność urządzenie Data Box dla dysku zarządzanego](media/data-box-deploy-ordered/order-managed-disks.png)

    Konto magazynu określone dla dysków zarządzanych jest używane jako przejściowe konto magazynu. Usługa Data Box przekazuje wirtualne dyski twarde jako stronicowe obiekty blob do przejściowego konta magazynu przed przekonwertowaniem ich na dyski zarządzane i przeniesieniem do grup zasobów. Aby uzyskać więcej informacji, zobacz [Weryfikowanie przekazania danych na platformę Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. W obszarze **Adres wysyłkowy** podaj swoje imię i nazwisko, nazwę oraz adres pocztowy firmy i prawidłowy numer telefonu. Kliknij pozycję **Zweryfikuj adres**. Usługa zweryfikuje adres wysyłkowy pod kątem dostępności usługi. Jeśli ta usługa jest dostępna dla podanego adresu wysyłkowego, otrzymasz odpowiednie powiadomienie.

   W przypadku wybrania opcji Wysyłka samozarządzanego otrzymasz powiadomienie e-mail po pomyślnym przekazaniu zamówienia. Aby uzyskać więcej informacji na temat samozarządzanej wysyłki, zobacz [Korzystanie z funkcji samozarządzanej wysyłki](data-box-portal-customer-managed-shipping.md).

8. Po pomyślnym sprawdzeniu szczegółów wysyłki kliknij przycisk **dalej** .

9. W obszarze **Szczegóły powiadomienia** podaj adresy e-mail. Usługa wysyła powiadomienia e-mail dotyczące wszystkich aktualizacji stanu zamówienia na określone adresy e-mail.

    Zalecamy użycie grupowego adresu e-mail, aby otrzymywać powiadomienia, jeśli administrator opuści grupę.

10. Zapoznaj się z informacjami z sekcji **Podsumowanie** związanymi z zamówieniem, kontaktami, powiadomieniami i postanowieniami dotyczącymi prywatności. Zaznacz pole oznaczające wyrażenie zgody na postanowienia dotyczące prywatności.

11. Kliknij pozycję **Zamów**. Utworzenie zamówienia trwa kilka minut.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Wykonaj następujące kroki, korzystając z interfejsu wiersza polecenia platformy Azure, aby zamówić urządzenie:

1. Zapisz ustawienia dla zamówienia urządzenie Data Box. Te ustawienia obejmują informacje osobiste/biznesowe, nazwę subskrypcji, informacje o urządzeniu i informacje o wysyłce. Te ustawienia będą potrzebne jako parametry podczas uruchamiania polecenia CLI w celu utworzenia kolejności urządzenie Data Box. W poniższej tabeli przedstawiono ustawienia parametrów używane dla `az databox job create` :

   | Ustawienie (parametr) | Opis |  Wartość przykładowa |
   |---|---|---|
   |resource-group| Użyj istniejącej grupy lub utwórz nową. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | myresourcegroup|
   |name| Nazwa tworzonego zamówienia. | "mydataboxorder"|
   |Nazwa kontaktu| Nazwa skojarzona z adresem wysyłkowym. | "GUS Polska"|
   |phone| Numer telefonu osoby lub firmy, która będzie otrzymywać zamówienie.| "14255551234"
   |location| Najbliższy region świadczenia usługi Azure, który będzie wysyłany do urządzenia.| "Zachodnie stany USA"|
   |sku| Określone urządzenie urządzenie Data Box, które ma być uporządkowane. Prawidłowe wartości to: "DataBox", "DataBoxDisk" i "DataBoxHeavy".| "DataBox" |
   |Lista adresów e-mail| Adresy e-mail skojarzone z kolejnością.| "gusp@contoso.com" |
   |Ulica — Address1| Ulica, do której zostanie wysłane zamówienie. | "15700 NE 39th St" |
   |Ulica — Address2| Informacje o adresie pomocniczym, takie jak numer apartamentu lub numer budynku. | "BLD 123" |
   |city| Miasto, do którego zostanie wysłane urządzenie. | Redmond |
   |Województwo lub Województwo| Stan, w którym urządzenie zostanie wysłane.| WA |
   |country| Kraj, w którym urządzenie zostanie wysłane. | "Stany Zjednoczone" |
   |kod pocztowy| Kod pocztowy kodu pocztowego skojarzony z adresem wysyłkowym.| "98052"|
   |Nazwa firmy| Nazwa używanej firmy.| "Contoso, LTD" |
   |konto magazynu| Konto usługi Azure Storage, z którego chcesz zaimportować dane.| mojekontomagazynu|
   |debugowanie| Dołącz informacje debugowania do pełnego rejestrowania  | --Debug |
   |Pomoc| Wyświetl informacje pomocy dla tego polecenia. | --Help-h |
   |tylko-show-Errors| Pokaż tylko błędy, pomijając ostrzeżenia. | --Only-show-Errors |
   |Wyjście-o| Ustawia format danych wyjściowych.  Dozwolone wartości: JSON, jsonc, None, Table, TSV, YAML, yamlc. Wartość domyślna to JSON. | --Output "JSON" |
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --zapytanie<string>|
   |tryb pełny| Uwzględnij pełne rejestrowanie. | --verbose |

2. W wierszu polecenia wyboru lub terminalu Użyj [AZ DATAbox Job Create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) , aby utworzyć zamówienie Azure Data Box.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Oto przykład użycia polecenia:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Oto dane wyjściowe uruchamiania polecenia:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. W przypadku wszystkich poleceń interfejsu wiersza polecenia platformy Azure formatowanie danych wyjściowych będzie domyślnie stosowane, chyba że zostanie on zmieniony. Można zmienić format danych wyjściowych za pomocą parametru globalnego `--output <output-format>` . Zmiana formatu na "Tabela" spowoduje zwiększenie czytelności danych wyjściowych.

   To samo polecenie zostało wykonane z niewielkim dostrajaniem, aby zmienić formatowanie:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Oto dane wyjściowe uruchamiania polecenia:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

---

## <a name="track-the-order"></a>Śledzenie zamówienia

# <a name="portal"></a>[Portal](#tab/portal)

Po złożeniu zamówienia możesz śledzić jego stan w witrynie Azure Portal. Przejdź do zamówienia urządzenia Data Box, a następnie przejdź do obszaru **Omówienie**, aby sprawdzić stan. Zamówienie wyświetlone w portalu ma stan **Zamówione**.

Jeśli urządzenie nie jest niedostępne, otrzymasz powiadomienie. Jeśli urządzenie jest dostępne, firma Microsoft zidentyfikuje urządzenie do wysłania i przygotuje wysyłkę. Podczas przygotowywania urządzenia są wykonywane następujące akcje:

* Dla każdego konta magazynu skojarzonego z urządzeniem tworzone są udziały SMB.
* Dla każdego udziału generowane są poświadczenia dostępu, takie jak nazwa użytkownika i hasło.
* Generowane jest również hasło urządzenia, które pomaga odblokować urządzenie.
* Urządzenie Data Box jest blokowane w celu uniemożliwienia nieupoważnionego dostępu do urządzenia w dowolnym momencie.

Po zakończeniu przygotowywania urządzenia w portalu zostanie wyświetlone zamówienie w stanie **Przetworzone**.

![Przetworzone zamówienie urządzenia Data Box](media/data-box-overview/data-box-order-status-processed.png)

Firma Microsoft następnie przygotowuje i wysyła urządzenie za pośrednictwem przewoźnika regionalnego. Po wysłaniu dysków otrzymasz numer służący do jego śledzenia. W portalu zamówienie zostanie wysłane ze stanem **Wysłane**.

![Wysłane zamówienie urządzenia Data Box](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="track-a-single-order"></a>Śledzenie pojedynczej kolejności

Aby uzyskać informacje o śledzeniu jednej istniejącej kolejności Azure Data Box, uruchom polecenie [AZ DATAbox Job show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show). Polecenie wyświetla informacje na temat zamówienia, takie jak, ale nie ograniczone do: nazwa, Grupa zasobów, informacje o śledzeniu, Identyfikator subskrypcji, informacje kontaktowe, typ wysyłki i jednostka SKU urządzenia.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   W poniższej tabeli przedstawiono informacje o parametrach `az databox job show` :

   | Parametr | Opis |  Wartość przykładowa |
   |---|---|---|
   |Grupa zasobów [wymagane]| Nazwa grupy zasobów skojarzonej z kolejnością. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | myresourcegroup|
   |Nazwa [wymagane]| Nazwa zamówienia, która ma zostać wyświetlona. | "mydataboxorder"|
   |debugowanie| Dołącz informacje debugowania do pełnego rejestrowania | --Debug |
   |Pomoc| Wyświetl informacje pomocy dla tego polecenia. | --Help-h |
   |tylko-show-Errors| Pokaż tylko błędy, pomijając ostrzeżenia. | --Only-show-Errors |
   |Wyjście-o| Ustawia format danych wyjściowych.  Dozwolone wartości: JSON, jsonc, None, Table, TSV, YAML, yamlc. Wartość domyślna to JSON. | --Output "JSON" |
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --zapytanie<string>|
   |tryb pełny| Uwzględnij pełne rejestrowanie. | --verbose |

   Oto przykład polecenia z formatem wyjściowym ustawionym na "Table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Oto dane wyjściowe uruchamiania polecenia:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> Kolejność list może być obsługiwana na poziomie subskrypcji, co sprawia, że grupa zasobów jest opcjonalnym parametrem (a nie wymaganym parametrem).

### <a name="list-all-orders"></a>Wyświetl listę wszystkich zamówień

Jeśli masz uporządkowaną wiele urządzeń, możesz uruchomić polecenie [AZ DATAbox Job list](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) , aby wyświetlić wszystkie zamówienia Azure Data Box. Polecenie wyświetla listę wszystkich zamówień należących do określonej grupy zasobów. Są również wyświetlane w danych wyjściowych: Nazwa zamówienia, stan wysyłki, region platformy Azure, typ dostawy, stan zamówienia. Anulowane zamówienia są również zawarte na liście.
Polecenie wyświetla również sygnatury czasowe poszczególnych zamówień.

```azurecli
az databox job list --resource-group <resource-group>
```

W poniższej tabeli przedstawiono informacje o parametrach `az databox job list` :

   | Parametr | Opis |  Wartość przykładowa |
   |---|---|---|
   |Grupa zasobów [wymagane]| Nazwa grupy zasobów zawierającej zamówienia. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | myresourcegroup|
   |debugowanie| Dołącz informacje debugowania do pełnego rejestrowania | --Debug |
   |Pomoc| Wyświetl informacje pomocy dla tego polecenia. | --Help-h |
   |tylko-show-Errors| Pokaż tylko błędy, pomijając ostrzeżenia. | --Only-show-Errors |
   |Wyjście-o| Ustawia format danych wyjściowych.  Dozwolone wartości: JSON, jsonc, None, Table, TSV, YAML, yamlc. Wartość domyślna to JSON. | --Output "JSON" |
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --zapytanie<string>|
   |tryb pełny| Uwzględnij pełne rejestrowanie. | --verbose |

   Oto przykład polecenia z formatem wyjściowym ustawionym na "Table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Oto dane wyjściowe uruchamiania polecenia:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

---

## <a name="cancel-the-order"></a>Anulowanie zamówienia

# <a name="portal"></a>[Portal](#tab/portal)

Aby anulować tę kolejność, w Azure Portal przejdź do **omówienia** i wybierz pozycję **Anuluj** na pasku poleceń.

Po złożeniu zamówienia możesz je anulować w dowolnym momencie, dopóki stan zamówienia nie zostanie oznaczony jako Przetworzone.

Aby usunąć anulowaną kolejność, przejdź do **omówienia** i wybierz pozycję **Usuń** z paska poleceń.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="cancel-an-order"></a>Anulowanie zamówienia

Aby anulować kolejność Azure Data Box, uruchom polecenie [AZ DATAbox Job Cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel). Musisz określić przyczynę anulowania zamówienia.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   W poniższej tabeli przedstawiono informacje o parametrach `az databox job cancel` :

   | Parametr | Opis |  Wartość przykładowa |
   |---|---|---|
   |Grupa zasobów [wymagane]| Nazwa grupy zasobów skojarzonej z kolejnością, która ma zostać usunięta. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | myresourcegroup|
   |Nazwa [wymagane]| Nazwa zamówienia do usunięcia. | "mydataboxorder"|
   |Przyczyna [wymagane]| Przyczyna anulowania zamówienia. | "Wprowadzono błędne informacje i są one niezbędne w celu anulowania zamówienia". |
   |tak| Nie Monituj o potwierdzenie. | --tak (-y)| --tak-y |
   |debugowanie| Dołącz informacje debugowania do pełnego rejestrowania | --Debug |
   |Pomoc| Wyświetl informacje pomocy dla tego polecenia. | --Help-h |
   |tylko-show-Errors| Pokaż tylko błędy, pomijając ostrzeżenia. | --Only-show-Errors |
   |Wyjście-o| Ustawia format danych wyjściowych.  Dozwolone wartości: JSON, jsonc, None, Table, TSV, YAML, yamlc. Wartość domyślna to JSON. | --Output "JSON" |
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --zapytanie<string>|
   |tryb pełny| Uwzględnij pełne rejestrowanie. | --verbose |

   Oto przykład polecenia z danymi wyjściowymi:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Oto dane wyjściowe uruchamiania polecenia:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Usuwanie zamówienia

Jeśli Azure Data Box zamówienie zostało anulowane, możesz uruchomić polecenie [AZ DATAbox Job Delete](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) , aby usunąć zamówienie.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   W poniższej tabeli przedstawiono informacje o parametrach `az databox job delete` :

   | Parametr | Opis |  Wartość przykładowa |
   |---|---|---|
   |Grupa zasobów [wymagane]| Nazwa grupy zasobów skojarzonej z kolejnością, która ma zostać usunięta. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | myresourcegroup|
   |Nazwa [wymagane]| Nazwa zamówienia do usunięcia. | "mydataboxorder"|
   |subskrypcja| Nazwa lub identyfikator (GUID) subskrypcji platformy Azure. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |tak| Nie Monituj o potwierdzenie. | --tak (-y)| --tak-y |
   |debugowanie| Dołącz informacje debugowania do pełnego rejestrowania | --Debug |
   |Pomoc| Wyświetl informacje pomocy dla tego polecenia. | --Help-h |
   |tylko-show-Errors| Pokaż tylko błędy, pomijając ostrzeżenia. | --Only-show-Errors |
   |Wyjście-o| Ustawia format danych wyjściowych.  Dozwolone wartości: JSON, jsonc, None, Table, TSV, YAML, yamlc. Wartość domyślna to JSON. | --Output "JSON" |
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --zapytanie<string>|
   |tryb pełny| Uwzględnij pełne rejestrowanie. | --verbose |

Oto przykład polecenia z danymi wyjściowymi:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Oto dane wyjściowe uruchamiania polecenia:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

---

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje o Azure Data Box artykułach, takich jak:

> [!div class="checklist"]
>
> * Wymagania wstępne dotyczące wdrażania usługi Data Box
> * Zamawianie urządzenia Data Box
> * Śledzenie zamówienia
> * Anulowanie zamówienia

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować usługę Data Box.

> [!div class="nextstepaction"]
> [Konfigurowanie usługi Azure Data Box](./data-box-deploy-set-up.md)

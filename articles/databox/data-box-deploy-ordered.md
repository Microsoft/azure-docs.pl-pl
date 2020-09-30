---
title: Samouczek przedstawiający kolejność Azure Data Box | Microsoft Docs
description: W tym samouczku dowiesz się, jak Azure Data Box, rozwiązanie hybrydowe, które umożliwia importowanie danych lokalnych na platformę Azure oraz kolejność Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: alkohli
ms.openlocfilehash: 3181b88b0cf49516eb5230585460d0cc91bb4042
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575303"
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

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

Możesz zalogować się do platformy Azure i uruchamiać polecenia interfejsu wiersza poleceń platformy Azure na jeden z dwóch sposobów:

* Interfejs wiersza polecenia można zainstalować i uruchomić polecenie CLI lokalnie.
* Poleceń interfejsu wiersza polecenia można uruchomić z poziomu Azure Portal w Azure Cloud Shell.

Użyjemy interfejsu wiersza polecenia platformy Azure za pomocą programu Windows PowerShell dla samouczka, ale możesz wybrać jedną z opcji.

### <a name="for-azure-cli"></a>Dla interfejsu wiersza polecenia platformy Azure

Przed rozpoczęciem upewnij się, że:

#### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

* Zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w wersji 2.0.67 lub nowszej. Alternatywnie możesz zainstalować plik [MSI](https://aka.ms/installazurecliwindows).

**Logowanie do platformy Azure**

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

**Zainstaluj rozszerzenie interfejsu wiersza polecenia Azure Data Box**

Aby można było użyć poleceń interfejsu wiersza polecenia Azure Data Box, należy zainstalować rozszerzenie. Rozszerzenia interfejsu wiersza polecenia platformy Azure dają dostęp do poleceń eksperymentalnych i w wersji wstępnej, które nie zostały jeszcze dostarczone jako część podstawowego interfejsu wiersza polecenia. Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [Korzystanie z rozszerzeń przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).

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

#### <a name="use-azure-cloud-shell"></a>Używanie usługi Azure Cloud Shell

Możesz użyć [Azure Cloud Shell](https://shell.azure.com/), hostowanego środowiska powłoki interaktywnej platformy Azure, za pomocą przeglądarki, aby uruchomić polecenia CLI. Azure Cloud Shell obsługuje bash lub Windows PowerShell z usługami platformy Azure. Interfejs wiersza polecenia platformy Azure jest wstępnie zainstalowany i skonfigurowany do użycia z Twoim kontem. Wybierz przycisk Cloud Shell w menu w prawej górnej części Azure Portal:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Przycisk uruchamia interaktywną powłokę, której można użyć, aby wykonać kroki opisane w tym artykule jak to zrobić.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Dla Azure PowerShell

Przed rozpoczęciem upewnij się, że:

* Zainstaluj program Windows PowerShell w wersji 6.2.4 lub nowszej.
* Zainstaluj Azure PowerShell (AZ) module.
* Zainstaluj Azure Data Box module (AZ. DataBox).
* Zaloguj się do platformy Azure.

#### <a name="install-azure-powershell-and-modules-locally"></a>Instalowanie Azure PowerShell i modułów lokalnie

**Instalowanie lub Uaktualnianie programu Windows PowerShell**

Konieczne będzie zainstalowanie programu Windows PowerShell w wersji 6.2.4 lub nowszej. Aby dowiedzieć się, jaka wersja programu PowerShell została zainstalowana, uruchom polecenie: `$PSVersionTable` .

Zostaną wyświetlone następujące dane wyjściowe:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Jeśli wersja jest starsza niż 6.2.4, należy uaktualnić wersję programu Windows PowerShell. Aby zainstalować najnowszą wersję programu Windows PowerShell, zobacz [install Azure PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7).

**Instalowanie modułów Azure PowerShell i urządzenie Data Box**

Musisz zainstalować moduły Azure PowerShell, aby użyć Azure PowerShell do uporządkowania Azure Data Box. Aby zainstalować moduły Azure PowerShell:

1. Zainstaluj [Azure PowerShell AZ module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).
2. Następnie zainstaluj AZ. DataBox za pomocą polecenia `Install-Module -Name Az.DataBox` .

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz okno polecenia programu Windows PowerShell i zaloguj się do platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount) :

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Oto dane wyjściowe z pomyślnego logowania:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Aby uzyskać szczegółowe informacje na temat sposobu logowania się do platformy Azure przy użyciu programu Windows PowerShell, zobacz [Logowanie za pomocą Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>Zamawianie urządzenia Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Aby zamówić urządzenie, wykonaj następujące czynności w Azure Portal.

1. Użyj swoich poświadczeń platformy Microsoft Azure, aby zalogować się pod następującym adresem URL: [https://portal.azure.com](https://portal.azure.com).
2. Wybierz pozycję **+ Utwórz zasób** i wyszukaj wartość *Azure Data Box*. Wybierz pozycję **Azure Data Box**.

   ![Zrzut ekranu przedstawiający nową sekcję z Azure Data Box w polu wyszukiwania.](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Wybierz pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający sekcję Azure Data Box z opcją tworzenia wywołana.](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Sprawdź, czy usługa Data Box jest dostępna w Twoim regionie. Wprowadź lub wybierz poniższe informacje, a następnie wybierz pozycję **Zastosuj**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Typ transferu     | Wybierz pozycję **Importuj na platformę Azure**.        |
    |Subskrypcja     | Wybierz subskrypcję EA, CSP lub dostęp sponsorowany Azure dla usługi Data Box. <br> Subskrypcja jest połączona z kontem rozliczeniowym.       |
    |Grupa zasobów | Wybierz istniejącą grupę zasobów. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. |
    |Kraj/region źródłowy    |    Wybierz kraj/region, w którym aktualnie znajdują się dane.         |
    |Docelowy region platformy Azure     |     Wybierz region platformy Azure, do którego chcesz przenieść dane. <br> Aby uzyskać więcej informacji, przejdź do sekcji [Dostępność regionalna](data-box-overview.md#region-availability).            |

    [![Azure Data Box zamówienie importu](media/data-box-deploy-ordered/select-data-box-import-04b.png)](media/data-box-deploy-ordered/select-data-box-import-04b.png#lightbox)

5. Wybierz pozycję **Data Box**. Maksymalna użyteczna pojemność dla pojedynczej kolejności wynosi 80 TB. W przypadku większych ilości danych możesz utworzyć wiele zamówień.

    ![Zrzut ekranu przedstawiający różne rozmiary danych, które są dostępne do wyboru: Data Box Disk 40 terabajtów, urządzenie Data Box 100 terabajtów, Data Box Heavy, 1000 terabajtów i wysyłania własnych dysków 1 terabajt.](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. Przejdź **do karty** **podstawowe informacje** . Wprowadź lub wybierz poniższe informacje, a następnie wybierz pozycję **Dalej:>miejsca docelowego danych **.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja      | Subskrypcja jest automatycznie wypełniana na podstawie wcześniejszego wyboru.|
    |Grupa zasobów    | Wybrana wcześniej Grupa zasobów. |
    |Nazwa zamówienia importu | Podaj przyjazną nazwę, aby śledzić zamówienie. <br> Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą.    |

    ![Zrzut ekranu przedstawiający kreatora kolejności, który zawiera informacje o poprawnym kroku kreatora z wypełnionymi informacjami.](media/data-box-deploy-ordered/select-data-box-import-06.png)

    Domyślnie hasło odblokowywania urządzenia jest szyfrowane przy użyciu klucza zarządzanego przez firmę Microsoft. Po zakończeniu zamówienia możesz dodać klucz zarządzany przez klienta. Klucz zarządzany przez klienta umożliwia użycie własnego klucza z klucza magazynu kluczy platformy Azure w celu ochrony hasła do odblokowywania urządzenia. Aby uzyskać więcej informacji, zobacz [Korzystanie z kluczy zarządzanych przez klienta w Azure Key Vault Azure Data Box](data-box-customer-managed-encryption-key-portal.md).

7. Na karcie **miejsce docelowe danych** wybierz pozycję **miejsce docelowe danych**.

    W przypadku używania **kont magazynu** jako miejsca docelowego magazynu zostanie wyświetlony następujący zrzut ekranu:

    ![Azure Data Box miejsce docelowe danych](media/data-box-deploy-ordered/select-data-box-import-07.png)

    W oparciu o wybrany region platformy Azure wybierz z listy filtrowanej istniejących kont magazynu co najmniej jedno z nich. Urządzenie Data Box można połączyć z maksymalnie 10 kontami magazynu. Można również utworzyć nowe **konto ogólnego przeznaczenia w wersji 1**, **konto ogólnego przeznaczenia w wersji 2** lub **konto usługi Blob Storage**.

    Konta magazynu z sieciami wirtualnymi są obsługiwane. Aby umożliwić usłudze Data Box współpracę z zabezpieczonymi kontami magazynu, włącz usługi zaufane w ustawieniach zapory sieciowej dla konta magazynu. Aby uzyskać więcej informacji, zobacz jak [dodać Azure Data Box jako zaufaną usługę](../storage/common/storage-network-security.md#exceptions).

    Jeśli przy użyciu urządzenie Data Box utworzyć **dyski zarządzane** na podstawie lokalnych wirtualnych dysków twardych (VHD), należy również podać następujące informacje:

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Grupy zasobów     | Utwórz nowe grupy zasobów, jeśli zamierzasz tworzyć dyski zarządzane na podstawie lokalnych dysków VHD. Istniejącej grupy zasobów można użyć tylko wtedy, gdy grupa zasobów została utworzona wcześniej podczas tworzenia kolejności urządzenie Data Box dla dysku zarządzanego przez usługę urządzenie Data Box. <br> Nazwy poszczególnych grup zasobów rozdziel średnikami. Obsługiwanych jest maksymalnie 10 grup zasobów.|

    ![Zrzut ekranu przedstawiający Kreatora zamawiania z krokami miejsca docelowego danych w Kreatorze z wypełnionymi poprawnymi informacjami.](media/data-box-deploy-ordered/select-data-box-import-07b.png)

    Konto magazynu określone dla dysków zarządzanych jest używane jako przejściowe konto magazynu. Usługa Data Box przekazuje wirtualne dyski twarde jako stronicowe obiekty blob do przejściowego konta magazynu przed przekonwertowaniem ich na dyski zarządzane i przeniesieniem do grup zasobów. Aby uzyskać więcej informacji, zobacz [Weryfikowanie przekazania danych na platformę Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).
   > [!NOTE]
   > Jeśli obiekt BLOB stronicowania nie zostanie pomyślnie przekonwertowany na dysk zarządzany, pozostaje na koncie magazynu i zostanie naliczona opłata za magazyn.

    Wybierz pozycję **Dalej: zabezpieczenia** , aby kontynuować.

1. W obszarze **zabezpieczenia**, jeśli chcesz włączyć podwójne szyfrowanie oparte na oprogramowaniu, wybierz pozycję **Włącz podwójne szyfrowanie dla zamówienia**. 

   Szyfrowanie oparte na oprogramowaniu jest wykonywane poza szyfrowaniem AES-256 bitowym danych na urządzenie Data Box.

   > [!NOTE]
   > Włączenie tej opcji może spowodować wydłużenie przetwarzania zamówień i kopiowania danych. Po utworzeniu zamówienia nie można zmienić tej opcji.

   ![Ekran zabezpieczenia dla importu pola danych, podwójne szyfrowanie](media/data-box-deploy-ordered/select-data-box-import-07c.png)

   Wybierz pozycję **Dalej: szczegóły kontaktu** , aby kontynuować.

8. W obszarze **szczegóły kontaktu**wybierz pozycję **+ Dodaj adres wysyłkowy**.

    ![Zrzut ekranu przedstawiający kreatora zamówień z etapem szczegóły kontaktu kreatora z opcją Dodaj adres wysyłkowy.](media/data-box-deploy-ordered/select-data-box-import-08a.png)

9. W obszarze **Adres wysyłkowy** podaj swoje imię i nazwisko, nazwę oraz adres pocztowy firmy i prawidłowy numer telefonu. Wybierz pozycję **Zweryfikuj adres**. Usługa zweryfikuje adres wysyłkowy pod kątem dostępności usługi. Jeśli ta usługa jest dostępna dla podanego adresu wysyłkowego, otrzymasz odpowiednie powiadomienie.

   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie adresu wysyłkowego z opcjami Wyślij przy użyciu i Dodaj adres wysyłkowy.](media/data-box-deploy-ordered/select-data-box-import-10.png)

   W przypadku wybrania opcji Wysyłka samozarządzanego otrzymasz powiadomienie e-mail po pomyślnym przekazaniu zamówienia. Aby uzyskać więcej informacji na temat samozarządzanej wysyłki, zobacz [Korzystanie z funkcji samozarządzanej wysyłki](data-box-portal-customer-managed-shipping.md).

10. Wybierz pozycję **Dodaj adres wysyłkowy** po pomyślnym sprawdzeniu szczegółów wysyłki. Nastąpi powrót do karty **szczegóły kontaktu** .

11. Po powrocie do **szczegółów kontaktowych** Dodaj jeden lub więcej adresów e-mail. Usługa wysyła powiadomienia e-mail dotyczące wszystkich aktualizacji stanu zamówienia na określone adresy e-mail.

    Zalecamy użycie grupowego adresu e-mail, aby otrzymywać powiadomienia, jeśli administrator opuści grupę.

    ![Zrzut ekranu przedstawiający sekcję adres E-mail kroku szczegóły kontaktu Kreatora zamawiania z polem tekstowym Adres E-mail i przycisk Dalej: recenzja i kolejność wywołana.](media/data-box-deploy-ordered/select-data-box-import-08c.png)

12. Zapoznaj się z informacjami w temacie **Przegląd + Order** (kolejność) związanych z warunkami zamówienia, kontaktu, powiadomień i prywatności. Zaznacz pole oznaczające wyrażenie zgody na postanowienia dotyczące prywatności.

13. Wybierz pozycję **Zamów**. Utworzenie zamówienia trwa kilka minut.

    ![Zrzut ekranu przedstawiający kreatora kolejności, pokazujący krok Recenzja i kolejność oraz opcję Order wywołana.](media/data-box-deploy-ordered/select-data-box-import-11.png)

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
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --zapytanie <string>|
   |tryb pełny| Uwzględnij pełne rejestrowanie. | --verbose |

2. W wierszu polecenia wyboru lub terminalu Użyj [zadania AZ Data Box Create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) , aby utworzyć zamówienie Azure Data Box.

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

# <a name="powershell"></a>[Program PowerShell](#tab/azure-ps)

Aby zamówić urządzenie, wykonaj następujące Azure PowerShell czynności:

1. Przed utworzeniem zamówienia importu należy uzyskać konto magazynu i zapisać obiekt konta magazynu w zmiennej.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Zapisz ustawienia dla zamówienia urządzenie Data Box. Te ustawienia obejmują informacje osobiste/biznesowe, nazwę subskrypcji, informacje o urządzeniu i informacje o wysyłce. Te ustawienia będą potrzebne jako parametry podczas uruchamiania polecenia programu PowerShell w celu utworzenia kolejności urządzenie Data Box. W poniższej tabeli przedstawiono ustawienia parametrów używane dla [New-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/New-AzDataBoxJob).

    | Ustawienie (parametr) | Opis |  Wartość przykładowa |
    |---|---|---|
    |ResourceGroupName [wymagane]| Użyj istniejącej grupy zasobów. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | myresourcegroup|
    |Nazwa [wymagane]| Nazwa tworzonego zamówienia. | "mydataboxorder"|
    |ContactName [wymagany]| Nazwa skojarzona z adresem wysyłkowym. | "GUS Polska"|
    |Numer telefonu [wymagany]| Numer telefonu osoby lub firmy, która będzie otrzymywać zamówienie.| "14255551234"
    |Lokalizacja [wymagana]| Najbliższy region świadczenia usługi Azure, który będzie wysyłany do urządzenia.| WestUS|
    |DataBoxType [wymagane]| Określone urządzenie urządzenie Data Box, które ma być uporządkowane. Prawidłowe wartości to: "DataBox", "DataBoxDisk" i "DataBoxHeavy".| "DataBox" |
    |EmailId [wymagane]| Adresy e-mail skojarzone z kolejnością.| "gusp@contoso.com" |
    |StreetAddress1 [wymagane]| Ulica, do której zostanie wysłane zamówienie. | "15700 NE 39th St" |
    |StreetAddress2| Informacje o adresie pomocniczym, takie jak numer apartamentu lub numer budynku. | "BLD 123" |
    |StreetAddress3| Informacje o adresie trzeciego adresu. | |
    |Miasto [wymagane]| Miasto, do którego zostanie wysłane urządzenie. | Redmond |
    |StateOrProvinceCode [wymagane]| Stan, w którym urządzenie zostanie wysłane.| WA |
    |CountryCode [wymagane]| Kraj, w którym urządzenie zostanie wysłane. | "Stany Zjednoczone" |
    |KodPocztowy [wymagane]| Kod pocztowy kodu pocztowego skojarzony z adresem wysyłkowym.| "98052"|
    |CompanyName| Nazwa używanej firmy.| "Contoso, LTD" |
    |StorageAccountResourceId [wymagane]| Identyfikator konta usługi Azure Storage, z którego chcesz zaimportować dane.| <AzStorageAccount>. Identyfikator |

3. W wierszu polecenia wyboru lub terminalu Użyj opcji [New-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/New-AzDataBoxJob) , aby utworzyć zamówienie Azure Data Box.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   Oto dane wyjściowe uruchamiania polecenia:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
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
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --zapytanie <string>|
   |tryb pełny| Uwzględnij pełne rejestrowanie. | --verbose |

   Oto przykład polecenia z formatem danych wyjściowych ustawionym na "Tabela":

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
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --zapytanie <string>|
   |tryb pełny| Uwzględnij pełne rejestrowanie. | --verbose |

   Oto przykład polecenia z formatem danych wyjściowych ustawionym na "Tabela":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Oto dane wyjściowe uruchamiania polecenia:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Śledzenie pojedynczej kolejności

Aby uzyskać informacje o śledzeniu jednej istniejącej kolejności Azure Data Box, uruchom polecenie [Get-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/Get-AzDataBoxJob). Polecenie wyświetla informacje na temat zamówienia, takie jak, ale nie ograniczone do: nazwa, Grupa zasobów, informacje o śledzeniu, Identyfikator subskrypcji, informacje kontaktowe, typ wysyłki i jednostka SKU urządzenia.

> [!NOTE]
> `Get-AzDataBoxJob` służy do wyświetlania jednego i wielu zamówień. Różnica polega na tym, że określasz nazwę zamówienia dla pojedynczych zamówień.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   W poniższej tabeli przedstawiono informacje o parametrach `Get-AzDataBoxJob` :

   | Parametr | Opis |  Wartość przykładowa |
   |---|---|---|
   |Resources [wymagane]| Nazwa grupy zasobów skojarzonej z kolejnością. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | myresourcegroup|
   |Nazwa [wymagane]| Nazwa zamówienia, dla którego mają zostać pobrane informacje. | "mydataboxorder"|
   |ResourceId| Identyfikator zasobu skojarzonego z kolejnością. |  |

   Oto przykład polecenia z danymi wyjściowymi:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Oto dane wyjściowe uruchamiania polecenia:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Wyświetl listę wszystkich zamówień

Jeśli masz uporządkowaną wiele urządzeń, możesz uruchomić polecenie [Get-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/Get-AzDataBoxJob) , aby wyświetlić wszystkie zamówienia Azure Data Box. Polecenie wyświetla listę wszystkich zamówień należących do określonej grupy zasobów. Są również wyświetlane w danych wyjściowych: Nazwa zamówienia, stan wysyłki, region platformy Azure, typ dostawy, stan zamówienia. Anulowane zamówienia są również zawarte na liście.
Polecenie wyświetla również sygnatury czasowe poszczególnych zamówień.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Oto przykładowe polecenie:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Oto dane wyjściowe uruchamiania polecenia:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
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
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --zapytanie <string>|
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
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --zapytanie <string>|
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

# <a name="powershell"></a>[Program PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Anulowanie zamówienia

Aby anulować kolejność Azure Data Box, uruchom polecenie [stop-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/stop-azdataboxjob). Musisz określić przyczynę anulowania zamówienia.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

W poniższej tabeli przedstawiono informacje o parametrach `Stop-AzDataBoxJob` :

| Parametr | Opis |  Wartość przykładowa |
|---|---|---|
|Resources [wymagane]| Nazwa grupy zasobów skojarzonej z kolejnością, aby została anulowana. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | myresourcegroup|
|Nazwa [wymagane]| Nazwa zamówienia do usunięcia. | "mydataboxorder"|
|Przyczyna [wymagane]| Przyczyna anulowania zamówienia. | "Wprowadzono błędne informacje i są one niezbędne w celu anulowania zamówienia". |
|Force | Wymusza uruchomienie polecenia cmdlet bez potwierdzenia przez użytkownika. | -Force |

Oto przykład polecenia z danymi wyjściowymi:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Oto dane wyjściowe uruchamiania polecenia:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Usuwanie zamówienia

Jeśli Azure Data Box zamówienie zostało anulowane, możesz uruchomić polecenie [Remove-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/remove-azdataboxjob) , aby usunąć zamówienie.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

W poniższej tabeli przedstawiono informacje o parametrach `Remove-AzDataBoxJob` :

| Parametr | Opis |  Wartość przykładowa |
|---|---|---|
|Resources [wymagane]| Nazwa grupy zasobów skojarzonej z kolejnością, która ma zostać usunięta. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | myresourcegroup|
|Nazwa [wymagane]| Nazwa zamówienia do usunięcia. | "mydataboxorder"|
|Force | Wymusza uruchomienie polecenia cmdlet bez potwierdzenia przez użytkownika. | -Force |

Oto przykład polecenia z danymi wyjściowymi:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Oto dane wyjściowe uruchamiania polecenia:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
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

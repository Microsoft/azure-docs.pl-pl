---
title: Samouczek dotyczący zamawiania Azure Data Box | Microsoft Docs
description: Z tego samouczka dowiesz się więcej Azure Data Box rozwiązania hybrydowego, które umożliwia importowanie danych lokalnych na platformę Azure, oraz sposobu zamawiania Azure Data Box.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: eae8cca0302993c16ea29adddf6e4ee9b5b24be8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770902"
---
# <a name="tutorial-order-azure-data-box"></a>Samouczek: Zamawianie urządzenia Azure Data Box

Azure Data Box to hybrydowe rozwiązanie, które umożliwia importowanie danych lokalnych na platformę Azure szybko, łatwo i bez problemów. Przesyłasz dane do dostarczonego przez firmę Microsoft urządzenia magazynującego o pojemności 80 TB (pojemności do wykorzystania), a następnie wysyłasz je z powrotem. Te dane są następnie przekazywane na platformę Azure.

W tym samouczku opisano sposób zamawiania usługi Azure Data Box. Ten samouczek zawiera informacje dotyczące:  

> [!div class="checklist"]
>
> * Wymagania wstępne dotyczące wdrażania usługi Data Box
> * Zamawianie usługi Data Box
> * Śledzenie zamówienia
> * Anulowanie zamówienia

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/portal)

Przed wdrożeniem urządzenia należy spełnić następujące wymagania wstępne urządzenie Data Box konfiguracji dla usługi i urządzenia:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

Możesz zalogować się do platformy Azure i uruchamiać polecenia interfejsu wiersza polecenia platformy Azure na jeden z dwóch sposobów:

* Możesz zainstalować interfejs wiersza polecenia i uruchamiać polecenia interfejsu wiersza polecenia lokalnie.
* Polecenia interfejsu wiersza polecenia można uruchamiać z poziomu Azure Portal, w Azure Cloud Shell.

W tym samouczku Windows PowerShell interfejs wiersza polecenia platformy Azure, ale możesz wybrać jedną z tych opcji.

### <a name="for-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Przed rozpoczęciem upewnij się, że:

#### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

* Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.67 lub nowszej. Alternatywnie możesz zainstalować program [przy użyciu pakietu MSI.](https://aka.ms/installazurecliwindows)

**Logowanie do platformy Azure**

Otwórz okno polecenia Windows PowerShell i zaloguj się do platformy Azure za pomocą [polecenia az login:](/cli/azure/reference-index#az_login)

```azurecli
PS C:\Windows> az login
```

Oto dane wyjściowe pomyślnego logowania:

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

**Instalowanie rozszerzenia interfejsu wiersza Azure Data Box interfejsu wiersza polecenia**

Aby można było używać Azure Data Box interfejsu wiersza polecenia, należy zainstalować rozszerzenie. Rozszerzenia interfejsu wiersza polecenia platformy Azure dają dostęp do poleceń eksperymentalnych i w wersji wstępnej, które nie zostały jeszcze dostarczone jako część podstawowego interfejsu wiersza polecenia. Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [Korzystanie z rozszerzeń w interfejsie wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).

Aby zainstalować rozszerzenie dla Azure Data Box, uruchom następujące polecenie: `az extension add --name databox` :

```azurecli

    PS C:\Windows> az extension add --name databox
```

Jeśli rozszerzenie zostało zainstalowane pomyślnie, zobaczysz następujące dane wyjściowe:

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

Aby uruchamiać polecenia [interfejsu wiersza polecenia,](https://shell.azure.com/)możesz użyć Azure Cloud Shell , czyli środowiska interaktywnej powłoki hostowanej na platformie Azure, za pośrednictwem przeglądarki. Azure Cloud Shell obsługuje powłokę Bash lub Windows PowerShell z usługami platformy Azure. Interfejs wiersza polecenia platformy Azure jest wstępnie zainstalowany i skonfigurowany do użycia z Twoim kontem. Wybierz Cloud Shell w menu w prawym górnym rogu strony Azure Portal:

![Cloud Shell menu](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Przycisk uruchamia interaktywną powłokę, która umożliwia uruchamianie kroków opisanych w tym artykule z instrukcjami.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Na Azure PowerShell

Przed rozpoczęciem upewnij się, że:

* Zainstaluj Windows PowerShell 6.2.4 lub wyższą.
* Zainstaluj Azure PowerShell (AZ).
* Zainstaluj Azure Data Box (Az.DataBox).
* Zaloguj się do platformy Azure.

#### <a name="install-azure-powershell-and-modules-locally"></a>Instalowanie Azure PowerShell modułów i modułów lokalnie

**Instalowanie lub uaktualnianie Windows PowerShell**

Musisz mieć zainstalowaną wersję Windows PowerShell 6.2.4 lub nowszą. Aby dowiedzieć się, jaka wersja programu PowerShell jest zainstalowana, uruchom: `$PSVersionTable` .

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

Jeśli twoja wersja jest niższa niż 6.2.4, musisz uaktualnić wersję Windows PowerShell. Aby zainstalować najnowszą wersję programu Windows PowerShell, zobacz [Instalowanie Azure PowerShell](/powershell/scripting/install/installing-powershell).

**Instalowanie Azure PowerShell i urządzenie Data Box modułów**

Konieczne będzie zainstalowanie modułów Azure PowerShell, aby Azure PowerShell porządek w Azure Data Box. Aby zainstalować Azure PowerShell modułów:

1. Zainstaluj moduł [Azure PowerShell Az.](/powershell/azure/new-azureps-module-az)
2. Następnie zainstaluj program Az.DataBox przy użyciu polecenia `Install-Module -Name Az.DataBox` .

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz okno Windows PowerShell polecenia i zaloguj się do platformy Azure za pomocą polecenia [Connect-AzAccount:](/powershell/module/az.accounts/Connect-AzAccount)

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Oto dane wyjściowe pomyślnego logowania:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Aby uzyskać szczegółowe informacje na temat logowania się do platformy Azure przy użyciu usługi Windows PowerShell, zobacz Logowanie się przy [użyciu Azure PowerShell](/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>Zamawianie urządzenia Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Wykonaj poniższe kroki w Azure Portal, aby zamówić urządzenie.

1. Użyj swoich poświadczeń platformy Microsoft Azure, aby zalogować się pod następującym adresem URL: [https://portal.azure.com](https://portal.azure.com).
2. Wybierz pozycję **+ Utwórz zasób** i wyszukaj wartość *Azure Data Box*. Wybierz pozycję **Azure Data Box**.

   ![Zrzut ekranu przedstawiający nową sekcję z Azure Data Box w polu wyszukiwania](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Wybierz przycisk **Utwórz**.

   ![Zrzut ekranu Azure Data Box sekcji z wywołaną opcją Utwórz](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Sprawdź, czy usługa Data Box jest dostępna w Twoim regionie. Wprowadź lub wybierz poniższe informacje, a następnie wybierz pozycję **Zastosuj**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Typ transferu     | Wybierz pozycję **Importuj na platformę Azure**.        |
    |Subskrypcja     | Wybierz subskrypcję EA, CSP lub dostęp sponsorowany Azure dla usługi Data Box. <br> Subskrypcja jest połączona z kontem rozliczeniowym.       |
    |Grupa zasobów | Wybierz istniejącą grupę zasobów. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. |
    |Kraj/region źródłowy    |    Wybierz kraj/region, w którym aktualnie znajdują się dane.         |
    |Docelowy region platformy Azure     |     Wybierz region platformy Azure, do którego chcesz przenieść dane. <br> Aby uzyskać więcej informacji, przejdź do sekcji [Dostępność regionalna](data-box-overview.md#region-availability).            |

    [![Rozpoczynanie Azure Data Box importowania ](media/data-box-deploy-ordered/select-data-box-import-04-b.png)](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. Wybierz pozycję **Data Box**. Maksymalna pojemność do wykorzystania dla pojedynczego zamówienia to 80 TB. W przypadku większych ilości danych możesz utworzyć wiele zamówień.

    ![Dostępne rozmiary danych: Data Box Disk, 40 terabajtów; urządzenie Data Box, 100 terabajtów; Data Box Heavy, 1000 terabajtów; Wysyłanie własnych dysków, 1 terabajt](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. W **obszarze** Order (Kolejność) przejdź **do karty Basics (Podstawy).** Wprowadź lub wybierz następujące informacje i wybierz pozycję **Dalej: Miejsce docelowe>**.

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Subskrypcja      | Subskrypcja jest wypełniana automatycznie na podstawie wcześniejszego wyboru.|
    |Grupa zasobów    | Wybrana wcześniej grupa zasobów. |
    |Importowanie nazwy zamówienia | Podaj przyjazną nazwę, aby śledzić zamówienie. <br> Nazwa może zawierać od 3 do 24 znaków, które mogą być literami, cyframi i łącznikami. <br> Nazwa musi zaczynać i kończyć się literą lub cyfrą.    |

    ![urządzenie Data Box importowania zamówienia, ekran Podstawowe z wypełnionymi prawidłowymi informacjami](media/data-box-deploy-ordered/select-data-box-import-06.png)

7. Na **ekranie Miejsce docelowe** danych wybierz pozycję **Miejsce docelowe danych** — konta magazynu lub dyski zarządzane.

    Jeśli używasz **kont magazynu** jako miejsca docelowego magazynu, zostanie wyświetlony następujący ekran:

    ![urządzenie Data Box kolejności, ekran Miejsce docelowe danych z wybranymi kontami magazynu](media/data-box-deploy-ordered/select-data-box-import-07.png)

    Na podstawie określonego regionu świadczenia usługi Azure wybierz co najmniej jedno konto magazynu z listy filtrowanych istniejących kont magazynu. Urządzenie Data Box można połączyć z maksymalnie 10 kontami magazynu. Można również utworzyć nowe **konto ogólnego przeznaczenia w wersji 1**, **konto ogólnego przeznaczenia w wersji 2** lub **konto usługi Blob Storage**.

   > [!NOTE]
   > - Jeśli wybierzesz pozycję Plik w witrynie Azure PremiumStorage, aprowizowany limit przydziału udziału konta magazynu zwiększy się do rozmiaru danych kopiowanych do udziałów plików. Po zwiększaniu limitu przydziału nie jest on ponownie dostosowywany, na przykład jeśli z jakiegoś powodu urządzenie Data Box nie może skopiować danych.
   > - Ten limit przydziału jest używany do rozliczeń. Po przesłaniu danych do centrum danych należy dostosować limit przydziału do swoich potrzeb. Aby uzyskać więcej informacji, zobacz [Informacje o rozliczeniach.](../../articles/storage/files/understanding-billing.md)

    Konta magazynu z sieciami wirtualnymi są obsługiwane. Aby umożliwić usłudze Data Box współpracę z zabezpieczonymi kontami magazynu, włącz usługi zaufane w ustawieniach zapory sieciowej dla konta magazynu. Aby uzyskać więcej informacji, zobacz jak [dodać Azure Data Box jako zaufaną usługę.](../storage/common/storage-network-security.md#exceptions)

    Jeśli używasz urządzenie Data Box do tworzenia **dysków** zarządzanych na podstawie lokalnych wirtualnych dysków twardych (VHD), musisz również podać następujące informacje:

    |Ustawienie  |Wartość  |
    |---------|---------|
    |Grupy zasobów     | Utwórz nowe grupy zasobów, jeśli zamierzasz tworzyć dyski zarządzane na podstawie lokalnych dysków VHD. Istniejącej grupy zasobów można używać tylko wtedy, gdy grupa zasobów została wcześniej utworzona podczas tworzenia urządzenie Data Box dla dysków zarządzanych przez usługę urządzenie Data Box zasobów. <br> Nazwy poszczególnych grup zasobów rozdziel średnikami. Obsługiwanych jest maksymalnie 10 grup zasobów.|

    ![urządzenie Data Box zaimportuj zamówienie, ekran Miejsce docelowe danych, Dyski zarządzane zaznaczone](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    Konto magazynu określone dla dysków zarządzanych jest używane jako przejściowe konto magazynu. Usługa Data Box przekazuje wirtualne dyski twarde jako stronicowe obiekty blob do przejściowego konta magazynu przed przekonwertowaniem ich na dyski zarządzane i przeniesieniem do grup zasobów. Aby uzyskać więcej informacji, zobacz [Weryfikowanie przekazania danych na platformę Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

   > [!NOTE]
   > Jeśli stronicowy obiekt blob nie zostanie pomyślnie przekonwertowany na dysk zarządzany, pozostanie on na koncie magazynu i zostanie naliczona opłata za magazyn.

8. Wybierz **pozycję Dalej: Zabezpieczenia,** aby kontynuować.

    Ekran **Zabezpieczenia** umożliwia korzystanie z własnego klucza szyfrowania i własnego urządzenia oraz udostępnianie haseł, a także wybranie podwójnego szyfrowania.

    Wszystkie ustawienia na **ekranie Zabezpieczenia** są opcjonalne. Jeśli nie zmienisz żadnych ustawień, będą stosowane ustawienia domyślne.

    ![Ekran zabezpieczeń kreatora urządzenie Data Box importowania zamówień](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. Jeśli chcesz użyć własnego klucza zarządzanego przez klienta, aby chronić klucz dostępu odblokowania dla nowego zasobu, rozwiń element **Typ szyfrowania.**

    Skonfigurowanie klucza zarządzanego przez klienta dla Azure Data Box jest opcjonalne. Domyślnie program urządzenie Data Box klucz zarządzany przez firmę Microsoft do ochrony klucza dostępu odblokowania.

    Klucz zarządzany przez klienta nie ma wpływu na sposób szyfrowania danych na urządzeniu. Klucz jest używany tylko do szyfrowania klucza dostępu odblokowania urządzenia.

    Jeśli nie chcesz używać klucza zarządzanego przez klienta, przejdź do kroku 15.

   ![Ekran zabezpieczeń przedstawiający ustawienia typu szyfrowania](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. Wybierz **pozycję Klucz zarządzany przez klienta** jako typ klucza. Następnie wybierz **pozycję Wybierz magazyn kluczy i klucz**.
   
    ![Ekran zabezpieczeń, ustawienia klucza zarządzanego przez klienta](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. W **bloku Wybierz klucz Azure Key Vault** subskrypcji subskrypcja jest wypełniana automatycznie.

    - W **przypadku usługi Key Vault** możesz wybrać istniejący magazyn kluczy z listy rozwijanej.

      ![Wybieranie klawisza z Azure Key Vault ekranu](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - Możesz również wybrać pozycję **Utwórz nowy,** aby utworzyć nowy magazyn kluczy. Na **ekranie Tworzenie magazynu** kluczy wprowadź grupę zasobów i nazwę magazynu kluczy. Upewnij się, **że włączono** **ochronę usuwania nie soft i przeczyszczania.** Zaakceptuj wszystkie pozostałe wartości domyślne, a następnie wybierz **pozycję Przejrzyj i utwórz.**

      ![Tworzenie nowego Azure Key Vault konfiguracji](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      Przejrzyj informacje dotyczące magazynu kluczy, a następnie wybierz pozycję **Utwórz**. Poczekaj kilka minut na ukończenie tworzenia magazynu kluczy.

      ![Nowy Azure Key Vault przeglądu aplikacji](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. W **chmurze Select key from Azure Key Vault**(Wybieranie klucza z magazynu kluczy) możesz wybrać istniejący klucz w magazynie kluczy.

    ![Wybierz istniejący klucz z Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    Jeśli chcesz utworzyć nowy klucz, wybierz pozycję **Utwórz nowy.** Należy użyć klucza RSA. Rozmiar może być 2048 lub większy. Wprowadź nazwę nowego klucza, zaakceptuj inne wartości domyślne, a następnie wybierz pozycję **Utwórz**.

      ![Tworzenie nowej opcji klucza](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      Otrzymasz powiadomienie o utworzeniu klucza w magazynie kluczy.

13. Wybierz wersję **klucza** do użycia, a następnie wybierz pozycję **Wybierz**.

      ![Nowy klucz utworzony w magazynie kluczy](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    Jeśli chcesz utworzyć nową wersję klucza, wybierz pozycję **Utwórz nową.**

    ![Otwieranie okna dialogowego tworzenia nowej wersji klucza](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    Wybierz ustawienia dla nowej wersji klucza, a następnie wybierz pozycję **Utwórz**.

    ![Tworzenie nowej wersji klucza](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    Ustawienia **Typ szyfrowania** na **ekranie Zabezpieczenia** pokazują magazyn kluczy i klucz.

    ![Klucz i magazyn kluczy dla klucza zarządzanego przez klienta](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. Wybierz tożsamość użytkownika, za pomocą których będziesz zarządzać dostępem do tego zasobu. Wybierz **pozycję Wybierz tożsamość użytkownika.** W panelu po prawej stronie wybierz subskrypcję i tożsamość zarządzaną do użycia. Następnie wybierz opcję **Wybierz**.

    Tożsamość zarządzana przypisana przez użytkownika to autonomiczny zasób platformy Azure, który może służyć do zarządzania wieloma zasobami. Aby uzyskać więcej informacji, zobacz [Typy tożsamości zarządzanych.](../active-directory/managed-identities-azure-resources/overview.md)  

    Jeśli musisz utworzyć nową tożsamość zarządzaną, postępuj zgodnie ze wskazówkami z tematu [Tworzenie, tworzenie,](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)usuwanie lub przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Portal .
    
    ![Wybieranie tożsamości użytkownika](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    Tożsamość użytkownika jest wyświetlana w **ustawieniach typu** szyfrowania.

    ![Wybrana tożsamość użytkownika wyświetlana w ustawieniach typu szyfrowania](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. Jeśli nie chcesz używać haseł generowanych przez system, które domyślnie Azure Data Box,  rozwiń na ekranie Zabezpieczenia rozwiń ustawienie Przynieź **własne** hasło.

    Hasła generowane przez system są bezpieczne i zalecane, chyba że organizacja wymaga inaczej.

    ![Rozwinięte opcje "Przynieź własne hasło" dla urządzenie Data Box zamówienia importu](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - Aby użyć własnego hasła dla nowego urządzenia, w ustawieniach preferencji dotyczących hasła urządzenia wybierz pozycję Użyj własnego hasła i wpisz hasło spełniające wymagania dotyczące zabezpieczeń. 
     
     Hasło musi być alfanumeryczne i zawierać od 12 do 15 znaków, z co najmniej jedną małą literą, jedną małą literą, jednym znakiem specjalnym i jedną liczbą. 

     - Dozwolone znaki specjalne: @ # - $ % ^ ! + = ; : _ ( )
     - Niedozwolone znaki: I L o O 0
   
     ![Opcje użycia własnego hasła urządzenia na ekranie Zabezpieczenia dla zamówienia urządzenie Data Box importu](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - Aby użyć własnych haseł dla udziałów:

   1. Aby **ustawić preferencję udostępniania haseł,** wybierz **pozycję Użyj własnych haseł,** a następnie wybierz **hasła dla udziałów.**
     
       ![Opcje używania własnych haseł udziału na ekranie Zabezpieczenia dla zamówienia urządzenie Data Box importu](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    1. Wpisz hasło dla każdego konta magazynu w kolejności. Hasło będzie używane we wszystkich udziałach dla konta magazynu.
    
       Hasło musi być alfanumeryczne i zawierać od 12 do 64 znaków, z co najmniej jedną małą literą, jedną małą literą, jednym znakiem specjalnym i jedną liczbą.

       - Dozwolone znaki specjalne: @ # - $ % ^ ! + = ; : _ ( )
       - Niedozwolone znaki: I L o O 0
     
    1. Aby użyć tego samego hasła dla wszystkich kont magazynu, wybierz **pozycję Kopiuj do wszystkich.** 

    1. Po zakończeniu wybierz pozycję **Zapisz.**
     
       ![Ekran wprowadzania haseł udziału dla zamówienia urządzenie Data Box importu](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

    Na **ekranie** Zabezpieczenia możesz użyć funkcji **Wyświetl lub zmień hasła,** aby zmienić hasła.

16. W **zabezpieczeniach**, jeśli chcesz włączyć programowe podwójne szyfrowanie, rozwiń pozycję Podwójne szyfrowanie **(dla środowisk** o wysokim poziomie zabezpieczeń) i wybierz opcję Włącz podwójne szyfrowanie dla **zamówienia**.

    ![Ekran zabezpieczeń urządzenie Data Box importowania danych, włączanie szyfrowania opartego na oprogramowaniu dla urządzenie Data Box klienta](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    Szyfrowanie programowe jest wykonywane oprócz 256-bitowego szyfrowania AES danych na urządzenie Data Box.

    > [!NOTE]
    > Włączenie tej opcji może sprawić, że przetwarzanie zamówień i kopiowanie danych będzie trwać dłużej. Nie można zmienić tej opcji po utworzeniu zamówienia.

    Wybierz **pozycję Dalej: Szczegóły kontaktu,** aby kontynuować.

17. W **szczegółach kontaktu** wybierz **pozycję + Dodaj adres wysyłkowy.**

    ![Na ekranie Szczegóły kontaktu dodaj adresy wysyłkowe do Azure Data Box zamówienia importu](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. W obszarze **Adres wysyłkowy** podaj swoje imię i nazwisko, nazwę oraz adres pocztowy firmy i prawidłowy numer telefonu. Wybierz pozycję **Zweryfikuj adres**. Usługa zweryfikuje adres wysyłkowy pod kątem dostępności usługi. Jeśli ta usługa jest dostępna dla podanego adresu wysyłkowego, otrzymasz odpowiednie powiadomienie.

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie adresu wysyłkowego z wywołaną opcją Ship using (Wysyłka przy użyciu) i Add shipping address (Dodaj adres wysyłkowy).](media/data-box-deploy-ordered/select-data-box-import-10.png)

    Jeśli wybrano samodzielną wysyłkę, po pomyślnym zsyłaniu zamówienia otrzymasz powiadomienie e-mail. Aby uzyskać więcej informacji na temat samodzielnej wysyłki, zobacz Temat Use self-managed shipping (Korzystanie [z samodzielnej wysyłki).](data-box-portal-customer-managed-shipping.md)

19. Po **pomyślnym weryfikacji** szczegółów wysyłki wybierz pozycję Dodaj adres wysyłkowy. Wrócisz do karty **Szczegóły** kontaktu.

20. Po powrocie do szczegółów **kontaktu** dodaj co najmniej jeden adres e-mail. Usługa wysyła powiadomienia e-mail dotyczące wszystkich aktualizacji stanu zamówienia na określone adresy e-mail.

    Zalecamy użycie grupowego adresu e-mail, aby otrzymywać powiadomienia, jeśli administrator opuści grupę.

    ![Sekcja Adres e-mail w sekcji Szczegóły kontaktu w kreatorze zamówienia](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. Zapoznaj się z informacjami w **te tematach Review + Order (Przeglądanie +** zamówienie) związanych z zamówieniem, kontaktem, powiadomieniami i postanowieniami dotyczącymi prywatności. Zaznacz pole oznaczające wyrażenie zgody na postanowienia dotyczące prywatności.

22. Wybierz pozycję **Zamów**. Utworzenie zamówienia trwa kilka minut.

    ![Ekran przeglądów i zamówień kreatora zamówienia](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zamówić urządzenie, wykonaj następujące kroki przy użyciu interfejsu wiersza polecenia platformy Azure:

1. Zapisz ustawienia dla urządzenie Data Box danych. Te ustawienia obejmują informacje osobiste/biznesowe, nazwę subskrypcji, informacje o urządzeniu i informacje wysyłkowe. Musisz użyć tych ustawień jako parametrów podczas uruchamiania polecenia interfejsu wiersza polecenia, aby utworzyć urządzenie Data Box kolejności. W poniższej tabeli przedstawiono ustawienia parametrów używane dla programu `az databox job create` :

   | Ustawienie (parametr) | Opis |  Wartość przykładowa |
   |---|---|---|
   |resource-group| Użyj istniejącej grupy lub utwórz nową. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | "myresourcegroup"|
   |name| Nazwa zamówienia, które tworzysz. | "mydataboxorder"|
   |contact-name| Nazwa skojarzona z adresem wysyłki. | "Gus Zięć"|
   |phone| Numer telefonu osoby lub firmy, która otrzyma zamówienie.| "14255551234"
   |location| Najbliższy region świadczenia usługi Azure, który będzie wysyłać urządzenie.| "Zachodnie stany USA"|
   |sku| Określone urządzenie Data Box, które zamówisz. Prawidłowe wartości to: "DataBox", "DataBoxDisk" i "DataBoxHeavy"| "DataBox" |
   |lista wiadomości e-mail| Adresy e-mail skojarzone z zamówieniem.| "gusp@contoso.com" |
   |ulica adres1| Ulica, do której zostanie wysłane zamówienie. | "15700 NE 39th St" |
   |ulica-adres2| Informacje o adresie pomocniczym, takie jak numer domu lub numer budynku. | "Building 123" (Budynek 123) |
   |city| Miasto, do których urządzenie zostanie wysłane. | "Redmond" |
   |stan lub województwo| Stan, w którym urządzenie zostanie wysłane.| "WA" |
   |country| Kraj, w którym urządzenie zostanie wysłane. | "Stany Zjednoczone" |
   |kod pocztowy| Kod pocztowy lub kod pocztowy skojarzony z adresem wysyłkowym.| "98052"|
   |nazwa firmy| Nazwa firmy, dla których pracujesz.| "Contoso, LTD" |
   |konto magazynu| Konto usługi Azure Storage, z którego chcesz zaimportować dane.| "mystorageaccount"|
   |debugowanie| Dołącz informacje debugowania, aby uzyskać pełne rejestrowanie  | --debug |
   |Pomoc| Wyświetl informacje pomocy dotyczące tego polecenia. | --help -h |
   |only-show-errors| Pokaż tylko błędy, pomijając ostrzeżenia. | --only-show-errors |
   |output -o| Ustawia format danych wyjściowych.  Dozwolone wartości: json, jsonc, none, table, tsv, yaml, yamlc. Wartość domyślna to JSON. | --output "json" |
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --query <string>|
   |tryb pełny| Dołącz pełne rejestrowanie. | --verbose |

2. W wierszu polecenia lub terminalu uruchom polecenie [az data box job create,](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-create) aby utworzyć Azure Data Box zamówienia.

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

   Oto dane wyjściowe uruchomienia polecenia:

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

3. Wszystkie polecenia interfejsu wiersza polecenia platformy Azure domyślnie będą używać formatu JSON jako danych wyjściowych, chyba że go zmienisz. Format danych wyjściowych można zmienić przy użyciu parametru globalnego `--output <output-format>` . Zmiana formatu na "tabela" poprawi czytelność danych wyjściowych.

   Oto to samo polecenie, które właśnie uruchomiliśmy, z niewielkim dostosowaniem w celu zmiany formatowania:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Oto dane wyjściowe uruchomienia polecenia:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-ps)

Wykonaj następujące kroki, Azure PowerShell aby zamówić urządzenie:

1. Przed utworzeniem zamówienia importu musisz uzyskać konto magazynu i zapisać obiekt konta magazynu w zmiennej.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Zapisz ustawienia dla urządzenie Data Box danych. Te ustawienia obejmują informacje osobiste/biznesowe, nazwę subskrypcji, informacje o urządzeniu i informacje wysyłkowe. Musisz użyć tych ustawień jako parametrów podczas uruchamiania polecenia programu PowerShell, aby utworzyć urządzenie Data Box kolejności. W poniższej tabeli przedstawiono ustawienia parametrów używane przez [parametr New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob).

    | Ustawienie (parametr) | Opis |  Wartość przykładowa |
    |---|---|---|
    |ResourceGroupName [Wymagane]| Użyj istniejącej grupy zasobów. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | "myresourcegroup"|
    |Nazwa [Wymagane]| Nazwa zamówienia, które tworzysz. | "mydataboxorder"|
    |ContactName [Wymagane]| Nazwa skojarzona z adresem wysyłki. | "Gus Zięć"|
    |PhoneNumber [wymagane]| Numer telefonu osoby lub firmy, która otrzyma zamówienie.| "14255551234"
    |Lokalizacja [wymagane]| Najbliższy region świadczenia usługi Azure, który będzie wysyłać urządzenie.| "WestUS"|
    |DataBoxType [Required]| Określone urządzenie Data Box urządzenia, które chcesz zamówić. Prawidłowe wartości to: "DataBox", "DataBoxDisk" i "DataBoxHeavy"| "DataBox" |
    |EmailId [Wymagane]| Adresy e-mail skojarzone z zamówieniem.| "gusp@contoso.com" |
    |StreetAddress1 [wymagane]| Adres ulicy, pod który zostanie wysłane zamówienie. | "15700 NE 39th St" |
    |StreetAddress2| Informacje o adresie pomocniczym, takie jak numer domu lub numer budynku. | "Building 123" (Budynek 123) |
    |StreetAddress3| Informacje o trzeciorzędnych adresach. | |
    |Miasto [wymagane]| Miasto, do których urządzenie zostanie wysłane. | "Redmond" |
    |StateOrProvinceCode [Wymagane]| Stan, w którym urządzenie zostanie wysłane.| "WA" |
    |CountryCode [Wymagane]| Kraj, w którym urządzenie zostanie wysłane. | "Stany Zjednoczone" |
    |Kod pocztowy [wymagane]| Kod pocztowy lub kod pocztowy skojarzony z adresem wysyłkowym.| "98052"|
    |CompanyName| Nazwa firmy, dla których pracujesz.| "Contoso, LTD" |
    |StorageAccountResourceId [Wymagane]| Identyfikator konta usługi Azure Storage, z którego chcesz zaimportować dane.| <AzStorageAccount>.id |

3. W wierszu polecenia lub terminalu użyj polecenia [New-AzDataBoxJob,](/powershell/module/az.databox/New-AzDataBoxJob) aby utworzyć Azure Data Box zamówienia.

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

   Oto dane wyjściowe uruchomienia polecenia:

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

![Zamówienie urządzenie Data Box, które zostało przetworzone](media/data-box-overview/data-box-order-status-processed.png)

Firma Microsoft następnie przygotowuje i wysyła urządzenie za pośrednictwem przewoźnika regionalnego. Po wysłaniu dysków otrzymasz numer służący do jego śledzenia. W portalu zamówienie zostanie wysłane ze stanem **Wysłane**.

![Wysłane urządzenie Data Box zamówienia](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="track-a-single-order"></a>Śledzenie pojedynczego zamówienia

Aby uzyskać informacje dotyczące śledzenia pojedynczej, istniejącej Azure Data Box kolejności, [`az databox job show`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-show) uruchom . Polecenie wyświetla informacje o zamówieniu, takie jak między innymi: nazwa, grupa zasobów, informacje śledzenia, identyfikator subskrypcji, informacje kontaktowe, typ przesyłki i sku urządzenia.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   W poniższej tabeli przedstawiono informacje o parametrach dla `az databox job show` :

   | Parametr | Opis |  Wartość przykładowa |
   |---|---|---|
   |grupa zasobów [wymagane]| Nazwa grupy zasobów skojarzonej z zamówieniem. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | "myresourcegroup"|
   |name [Wymagane]| Nazwa zamówienia do wyświetlania. | "mydataboxorder"|
   |debugowanie| Dołączanie informacji debugowania w celu ujednliwego rejestrowania | --debug |
   |Pomoc| Wyświetl informacje pomocy dotyczące tego polecenia. | --help -h |
   |only-show-errors| Pokaż tylko błędy, pomijając ostrzeżenia. | --only-show-errors |
   |output -o| Ustawia format danych wyjściowych.  Dozwolone wartości: json, jsonc, none, table, tsv, yaml, yamlc. Wartość domyślna to JSON. | --output "json" |
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --query <string>|
   |tryb pełny| Dołącz pełne rejestrowanie. | --verbose |

   Oto przykład polecenia z formatem danych wyjściowych ustawionym na "table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Oto dane wyjściowe uruchomienia polecenia:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> Kolejność listy może być obsługiwana na poziomie subskrypcji i sprawia, że grupa zasobów jest opcjonalnym parametrem (a nie wymaganym parametrem).

### <a name="list-all-orders"></a>Lista wszystkich zamówień

Jeśli zamówino wiele urządzeń, możesz uruchomić polecenia , aby wyświetlić wszystkie [`az databox job list`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-list) Azure Data Box zamówień. Polecenie wyświetla listę wszystkich zamówień należących do określonej grupy zasobów. Wyświetlane również w danych wyjściowych: nazwa zamówienia, stan wysyłki, region świadczenia usługi Azure, typ dostawy, stan zamówienia. Anulowane zamówienia są również uwzględniane na liście.
Polecenie wyświetla również sygnatury czasowe każdego zamówienia.

```azurecli
az databox job list --resource-group <resource-group>
```

W poniższej tabeli przedstawiono informacje o parametrach dla `az databox job list` :

   | Parametr | Opis |  Wartość przykładowa |
   |---|---|---|
   |grupa zasobów [wymagane]| Nazwa grupy zasobów zawierającej zamówienia. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | "myresourcegroup"|
   |debugowanie| Dołącz informacje debugowania, aby uzyskać pełne rejestrowanie | --debug |
   |Pomoc| Wyświetl informacje pomocy dotyczące tego polecenia. | --help -h |
   |only-show-errors| Pokaż tylko błędy, pomijając ostrzeżenia. | --only-show-errors |
   |dane wyjściowe -o| Ustawia format danych wyjściowych.  Dozwolone wartości: json, jsonc, none, table, tsv, yaml, yamlc. Wartość domyślna to JSON. | --output "json" |
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --query <string>|
   |tryb pełny| Dołącz pełne rejestrowanie. | --verbose |

   Oto przykład polecenia z formatem danych wyjściowych ustawionym na "table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Oto dane wyjściowe uruchomienia polecenia:

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

### <a name="track-a-single-order"></a>Śledzenie pojedynczego zamówienia

Aby uzyskać informacje dotyczące śledzenia pojedynczego, istniejącego Azure Data Box, uruchom [get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob). Polecenie wyświetla informacje o zamówieniu, takie jak między innymi: nazwa, grupa zasobów, informacje śledzenia, identyfikator subskrypcji, informacje kontaktowe, typ przesyłki i sku urządzenia.

> [!NOTE]
> `Get-AzDataBoxJob` Służy do wyświetlania zarówno jednego, jak i wielu zamówień. Różnica polega na tym, że należy określić nazwę zamówienia dla pojedynczych zamówień.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   W poniższej tabeli przedstawiono informacje o parametrach dla `Get-AzDataBoxJob` :

   | Parametr | Opis |  Wartość przykładowa |
   |---|---|---|
   |ResourceGroup [wymagane]| Nazwa grupy zasobów skojarzonej z zamówieniem. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | "myresourcegroup"|
   |Nazwa [Wymagane]| Nazwa zamówienia, dla których mają być zawarte informacje. | "mydataboxorder"|
   |ResourceId| Identyfikator zasobu skojarzonego z zamówieniem. |  |

   Oto przykład polecenia z danych wyjściowych:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Oto dane wyjściowe uruchomienia polecenia:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Lista wszystkich zamówień

Jeśli zamówino wiele urządzeń, możesz uruchomić polecenia , aby wyświetlić wszystkie Azure Data Box [`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob) zamówień. Polecenie wyświetla listę wszystkich zamówień należących do określonej grupy zasobów. Wyświetlane również w danych wyjściowych: nazwa zamówienia, stan wysyłki, region świadczenia usługi Azure, typ dostawy, stan zamówienia. Anulowane zamówienia są również uwzględniane na liście.
Polecenie wyświetla również sygnatury czasowe każdego zamówienia.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Oto przykładowe polecenie:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Oto dane wyjściowe uruchomienia polecenia:

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

Aby anulować to zamówienie, w Azure Portal wybierz pozycję **Przegląd** i wybierz pozycję **Anuluj** na pasku poleceń.

Po złożeniu zamówienia możesz je anulować w dowolnym momencie, dopóki stan zamówienia nie zostanie oznaczony jako Przetworzone.

Aby usunąć anulowane zamówienie, przejdź do **strony Przegląd** i wybierz **pozycję Usuń** na pasku poleceń.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="cancel-an-order"></a>Anulowanie zamówienia

Aby anulować zamówienie Azure Data Box, [`az databox job cancel`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-cancel) uruchom . Musisz określić przyczynę anulowania zamówienia.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   W poniższej tabeli przedstawiono informacje o parametrach dla `az databox job cancel` :

   | Parametr | Opis |  Wartość przykładowa |
   |---|---|---|
   |grupa zasobów [wymagane]| Nazwa grupy zasobów skojarzonej z zamówieniem do usunięcia. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | "myresourcegroup"|
   |name [Wymagane]| Nazwa zamówienia do usunięcia. | "mydataboxorder"|
   |przyczyna [Wymagane]| Przyczyna anulowania zamówienia. | "Wprowadzono błędne informacje i muszę anulować zamówienie". |
   |tak| Nie monituj o potwierdzenie. | --yes (-y)| 
   |debugowanie| Dołączanie informacji debugowania w celu ujednania rejestrowania | --debug |
   |Pomoc| Wyświetl informacje pomocy dotyczące tego polecenia. | --help -h |
   |only-show-errors| Pokaż tylko błędy, pomijając ostrzeżenia. | --only-show-errors |
   |dane wyjściowe -o| Ustawia format danych wyjściowych.  Dozwolone wartości: json, jsonc, none, table, tsv, yaml, yamlc. Wartość domyślna to JSON. | --output "json" |
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --query <string>|
   |tryb pełny| Dołącz pełne rejestrowanie. | --verbose |

   Oto przykład polecenia z danych wyjściowych:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Oto dane wyjściowe uruchomienia polecenia:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Usuwanie zamówienia

Jeśli anulowano zamówienie Azure Data Box, możesz uruchomić, aby [`az databox job delete`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-delete) je usunąć.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   W poniższej tabeli przedstawiono informacje o parametrach dla `az databox job delete` :

   | Parametr | Opis |  Wartość przykładowa |
   |---|---|---|
   |grupa zasobów [wymagane]| Nazwa grupy zasobów skojarzonej z zamówieniem do usunięcia. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | "myresourcegroup"|
   |name [Wymagane]| Nazwa zamówienia do usunięcia. | "mydataboxorder"|
   |subskrypcja| Nazwa lub identyfikator (GUID) subskrypcji platformy Azure. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |tak| Nie monituj o potwierdzenie. | --yes (-y)|
   |debugowanie| Dołączanie informacji debugowania w celu ujednania rejestrowania | --debug |
   |Pomoc| Wyświetl informacje pomocy dotyczące tego polecenia. | --help -h |
   |only-show-errors| Pokaż tylko błędy, pomijając ostrzeżenia. | --only-show-errors |
   |output -o| Ustawia format danych wyjściowych.  Dozwolone wartości: json, jsonc, none, table, tsv, yaml, yamlc. Wartość domyślna to JSON. | --output "json" |
   |query| Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz [JMESPath](http://jmespath.org/). | --query <string>|
   |tryb pełny| Dołącz pełne rejestrowanie. | --verbose |

Oto przykład polecenia z danych wyjściowych:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Oto dane wyjściowe uruchomienia polecenia:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Anulowanie zamówienia

Aby anulować Azure Data Box, uruchom [element Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob). Musisz określić przyczynę anulowania zamówienia.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

W poniższej tabeli przedstawiono informacje o parametrach dla `Stop-AzDataBoxJob` :

| Parametr | Opis |  Wartość przykładowa |
|---|---|---|
|ResourceGroup [wymagane]| Nazwa grupy zasobów skojarzonej z zamówieniem do anulowania. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | "myresourcegroup"|
|Nazwa [Wymagane]| Nazwa zamówienia do usunięcia. | "mydataboxorder"|
|Przyczyna [wymagane]| Przyczyna anulowania zamówienia. | "Wprowadzono błędne informacje i muszę anulować zamówienie". |
|Force | Wymusza uruchomienie polecenia cmdlet bez potwierdzenia przez użytkownika. | -Force |

Oto przykład polecenia z danych wyjściowych:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Oto dane wyjściowe uruchomienia polecenia:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Usuwanie zamówienia

Jeśli anulowano zamówienie Azure Data Box, możesz uruchomić, aby [`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob) usunąć zamówienie.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

W poniższej tabeli przedstawiono informacje o parametrach dla `Remove-AzDataBoxJob` :

| Parametr | Opis |  Wartość przykładowa |
|---|---|---|
|ResourceGroup [wymagane]| Nazwa grupy zasobów skojarzonej z zamówieniem do usunięcia. Grupa zasobów to kontener logiczny zasobów, które mogą być zarządzane lub wdrażane razem. | "myresourcegroup"|
|Nazwa [Wymagane]| Nazwa zamówienia do usunięcia. | "mydataboxorder"|
|Force | Wymusza uruchomienie polecenia cmdlet bez potwierdzenia przez użytkownika. | -Force |

Oto przykład polecenia z danych wyjściowych:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Oto dane wyjściowe uruchomienia polecenia:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Następne kroki

W tym samouczku zadano informacje na temat Azure Data Box artykułów, takich jak:

> [!div class="checklist"]
>
> * Wymagania wstępne dotyczące wdrażania usługi Data Box
> * Zamawianie urządzenia Data Box
> * Śledzenie zamówienia
> * Anulowanie zamówienia

Przejdź do następnego samouczka, aby dowiedzieć się, jak skonfigurować usługę Data Box.

> [!div class="nextstepaction"]
> [Konfigurowanie usługi Azure Data Box](./data-box-deploy-set-up.md)

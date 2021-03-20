---
title: Utrwalanie plików w Azure Cloud Shell | Microsoft Docs
description: Wskazówki dotyczące sposobu, w jaki Azure Cloud Shell utrwalać pliki.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: f1846c126e81ca5851cfbb1d782e5315ae10a82a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152261"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Utrwalanie plików w Azure Cloud Shell
Cloud Shell korzysta z usługi Azure File Storage, aby utrwalać pliki między sesjami. Na początku, Cloud Shell prosi o skojarzenie nowego lub istniejącego udziału plików w celu utrwalenia plików między sesjami.

> [!NOTE]
> Bash i PowerShell współużytkują ten sam udział plików. Tylko jeden udział plików może być skojarzony z automatycznym instalowaniem w Cloud Shell.

> [!NOTE]
> Zapora magazynu Azure nie jest obsługiwana dla kont magazynu usługi Cloud Shell.

## <a name="create-new-storage"></a>Tworzenie nowego magazynu

W przypadku korzystania z ustawień podstawowych i wybierania tylko subskrypcji program Cloud Shell tworzy trzy zasoby w Twoim imieniu w obsługiwanym regionie, który jest najbliżej Ciebie:
* Grupa zasobów: `cloud-shell-storage-<region>`
* Konto magazynu: `cs<uniqueGuid>`
* Udział plików: `cs-<user>-<domain>-com-<uniqueGuid>`

![Ustawienie subskrypcji](media/persisting-shell-storage/basic-storage.png)

Udział plików jest instalowany jako `clouddrive` znajdujący się w `$Home` katalogu. Jest to jednorazowa akcja, a udział plików jest instalowany automatycznie w kolejnych sesjach. 

Udział plików zawiera również obraz 5 GB, który jest tworzony, co powoduje automatyczne utrwalanie danych w `$Home` katalogu. Dotyczy to zarówno bash, jak i programu PowerShell.

## <a name="use-existing-resources"></a>Korzystanie z istniejących zasobów

Za pomocą opcji Zaawansowane można kojarzyć istniejące zasoby. W przypadku wybrania regionu Cloud Shell należy wybrać konto magazynu zapasowego znajdujące się w tym samym regionie. Na przykład jeśli przypisany region to zachodnie stany USA, należy skojarzyć udział plików, który znajduje się w regionie zachodnie stany USA.

Po wyświetleniu monitu instalacji magazynu wybierz pozycję **Pokaż ustawienia zaawansowane** , aby wyświetlić dodatkowe opcje. Wypełnione opcje magazynu filtru dla magazynu lokalnie nadmiarowego (LRS), magazynu geograficznie nadmiarowego (GRS) i magazynu Strefowo nadmiarowego (ZRS). 

> [!NOTE]
> Korzystanie z kont magazynu GRS lub ZRS jest zalecane w celu uzyskania dodatkowej odporności dla zapasowego udziału plików. Który typ nadmiarowości zależy od celów i preferencji cen. [Dowiedz się więcej na temat opcji replikacji dla kont usługi Azure Storage](../storage/common/storage-redundancy.md).

![Ustawienie grupy zasobów](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Zabezpieczanie dostępu do magazynu
Aby zapewnić bezpieczeństwo, każdy użytkownik powinien obsługiwać własne konto magazynu.  W przypadku kontroli dostępu opartej na rolach (Azure RBAC) użytkownicy muszą mieć dostęp współautora lub wyższy na poziomie konta magazynu.

Cloud Shell używa udziału plików platformy Azure na koncie magazynu w ramach określonej subskrypcji. Ze względu na uprawnienia dziedziczone użytkownicy mający wystarczające prawa dostępu do subskrypcji będą mogli uzyskiwać dostęp do wszystkich kont magazynu oraz udziałów plików zawartych w subskrypcji.

Użytkownicy powinni zablokować dostęp do swoich plików, ustawiając uprawnienia na koncie magazynu lub na poziomie subskrypcji.

## <a name="supported-storage-regions"></a>Obsługiwane regiony magazynu
Aby znaleźć bieżący region, możesz uruchomić program `env` w bash i zlokalizować zmienną `ACC_LOCATION` lub z poziomu programu PowerShell `$env:ACC_LOCATION` . Udziały plików otrzymują obraz 5 GB utworzony w celu utrwalenia `$Home` katalogu.

Maszyny Cloud Shell istnieją w następujących regionach:

|Warstwowy|Region (Region)|
|---|---|
|Ameryka Północna i Południowa|Wschodnie stany USA, Południowo-środkowe stany USA, zachodnie stany USA|
|Europa|Europa Północna, Europa Zachodnia|
|Azja i Pacyfik|Indie Środkowe, Azja Południowo-Wschodnia|

Klienci powinni wybrać region podstawowy, chyba że mają wymóg, aby ich dane były przechowywane w określonym regionie. Jeśli mają takie wymagania, należy użyć pomocniczego regionu magazynu.

### <a name="secondary-storage-regions"></a>Pomocnicze regiony magazynu
Jeśli jest używany pomocniczy region magazynu, skojarzone konto usługi Azure Storage znajduje się w innym regionie niż maszyna Cloud Shell, do której są one instalowane. Na przykład Jan może ustawić swoje konto magazynu, które ma znajdować się w Kanadzie wschód, region pomocniczy, ale maszyna, do której jest zainstalowana, nadal znajduje się w regionie podstawowym. Dane przechowywane w stanie spoczynku znajdują się w Kanadzie, ale są przetwarzane w Stany Zjednoczone.

> [!NOTE]
> W przypadku korzystania z regionu pomocniczego dostęp do plików i czas uruchamiania Cloud Shell mogą być wolniejsze.

Użytkownik może uruchomić program `(Get-CloudDrive | Get-AzStorageAccount).Location` PowerShell, aby zobaczyć lokalizację ich udziału plików.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Ograniczanie tworzenia zasobów przy użyciu zasad zasobów platformy Azure
Konta magazynu tworzone w Cloud Shell są oznaczone przy użyciu `ms-resource-usage:azure-cloud-shell` . Jeśli chcesz uniemożliwić użytkownikom tworzenie kont magazynu w Cloud Shell, Utwórz [zasady zasobów platformy Azure dla tagów](../governance/policy/samples/index.md) , które są wyzwalane przez ten konkretny tag.

## <a name="how-cloud-shell-storage-works"></a>Jak działa Cloud Shell Storage 
Cloud Shell utrzymuje pliki przy użyciu obu następujących metod: 
* Tworzenie obrazu dysku `$Home` katalogu w celu utrwalenia całej zawartości w katalogu. Obraz dysku jest zapisywany w określonym udziale plików jako `acc_<User>.img` o godzinie `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` i automatycznie synchronizuje zmiany. 
* Zainstalowanie określonego udziału plików jako `clouddrive` `$Home` katalogu w celu bezpośredniej interakcji z udziałem plików. `/Home/<User>/clouddrive` jest mapowany na `fileshare.storage.windows.net/fileshare` .
 
> [!NOTE]
> Wszystkie pliki w `$Home` katalogu, takie jak klucze SSH, są utrwalane w obrazie dysku użytkownika, który jest przechowywany w zainstalowanym udziale plików. Stosuj najlepsze rozwiązania w przypadku utrwalania informacji w `$Home` katalogu i zainstalowanego udziału plików.

## <a name="clouddrive-commands"></a>polecenia CloudDrive

### <a name="use-the-clouddrive-command"></a>Użyj `clouddrive` polecenia
W Cloud Shell można uruchomić polecenie o nazwie `clouddrive` , które pozwala ręcznie zaktualizować udział plików zainstalowany w programie Cloud Shell.

![Uruchamianie polecenia "CloudDrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Staw `clouddrive`
Aby wykryć, który udział plików został zainstalowany jako `clouddrive` , uruchom `df` polecenie. 

Ścieżka do pliku CloudDrive zawiera nazwę konta magazynu i udział plików w adresie URL. Na przykład `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Zainstaluj nowy CloudDrive

#### <a name="prerequisites-for-manual-mounting"></a>Wymagania wstępne dotyczące ręcznego instalowania
Udział plików skojarzony z Cloud Shell można zaktualizować przy użyciu `clouddrive mount` polecenia.

W przypadku instalowania istniejącego udziału plików konta magazynu muszą znajdować się w regionie wybierania Cloud Shell. Pobierz lokalizację, uruchamiając `env` i sprawdzając `ACC_LOCATION` .

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount`Polecenie

> [!NOTE]
> Jeśli instalujesz nowy udział plików, dla katalogu zostanie utworzony nowy obraz użytkownika `$Home` . Poprzedni `$Home` obraz jest przechowywany w poprzednim udziale plików.

Uruchom `clouddrive mount` polecenie z następującymi parametrami:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Aby wyświetlić więcej szczegółów, uruchom `clouddrive mount -h` polecenie, jak pokazano poniżej:

![Uruchamianie CloudDrive mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Odinstaluj CloudDrive
Można odinstalować udział plików, który jest zainstalowany do Cloud Shell w dowolnym momencie. Ponieważ Cloud Shell wymaga użycia zainstalowanego udziału plików, zostanie wyświetlony monit o utworzenie i zainstalowanie kolejnego udziału plików podczas następnej sesji.

1. Uruchom polecenie `clouddrive unmount`.
2. Potwierdzanie i Potwierdzanie zatwierdzeń.

Udział plików będzie nadal istnieć, chyba że zostanie usunięty ręcznie. Cloud Shell nie będzie już wyszukiwać tego udziału plików na kolejnych sesjach. Aby wyświetlić więcej szczegółów, uruchom `clouddrive unmount -h` polecenie, jak pokazano poniżej:

![Uruchamianie CloudDrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Chociaż uruchomienie tego polecenia nie spowoduje usunięcia żadnych zasobów, ręczne usunięcie grupy zasobów, konta magazynu lub udziału plików, który jest mapowany do Cloud Shell wymazuje `$Home` obraz dysku katalogu i wszystkie pliki w udziale plików. Nie można tego cofnąć.
## <a name="powershell-specific-commands"></a>Polecenia specyficzne dla programu PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Wyświetl listę `clouddrive` udziałów plików platformy Azure
`Get-CloudDrive`Polecenie cmdlet pobiera informacje o udziale plików platformy Azure aktualnie zainstalowane przez program `clouddrive` w Cloud Shell. <br>
![Uruchamianie Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Odinstaluj `clouddrive`
Można odinstalować udział plików platformy Azure, który jest instalowany w Cloud Shell w dowolnym momencie. Jeśli udział plików platformy Azure został usunięty, zostanie wyświetlony monit o utworzenie i zainstalowanie nowego udziału plików platformy Azure podczas kolejnej sesji.

`Dismount-CloudDrive`Polecenie cmdlet Odinstalowuje udział plików platformy Azure z bieżącego konta magazynu. Odinstalowanie `clouddrive` kończy bieżącą sesję. Użytkownik zostanie poproszony o utworzenie i zainstalowanie nowego udziału plików platformy Azure podczas kolejnej sesji.
![Uruchamianie programu Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Uwaga: Jeśli musisz zdefiniować funkcję w pliku i wywołać ją z poleceń cmdlet programu PowerShell, należy uwzględnić operator kropki. Na przykład:. .\MyFunctions.ps1

## <a name="next-steps"></a>Następne kroki
[Cloud Shell — Szybki Start](quickstart.md) <br>
[Dowiedz się więcej o usłudze Microsoft Azure Files Storage](../storage/files/storage-files-introduction.md) <br>
[Informacje o tagach magazynu](../azure-resource-manager/management/tag-resources.md) <br>
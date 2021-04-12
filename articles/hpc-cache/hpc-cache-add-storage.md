---
title: Dodawanie magazynu do pamięci podręcznej platformy Azure HPC
description: Jak zdefiniować cele magazynu, aby pamięć podręczna platformy Azure HPC mogła używać lokalnego systemu plików NFS lub kontenerów obiektów blob platformy Azure do przechowywania długoterminowych plików
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 44b2534d7aeb12f4819a6c42cfb29d057ce26ddc
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259034"
---
# <a name="add-storage-targets"></a>Dodawanie lokalizacji docelowych magazynu

*Cele magazynu* są magazynem zaplecza dla plików, do których dostęp odbywa się za pomocą pamięci podręcznej platformy Azure HPC. Można dodać magazyn systemu plików NFS (taki jak lokalny system sprzętowy) lub zapisać dane w usłudze Azure Blob.

Można zdefiniować maksymalnie 20 różnych miejsc docelowych magazynu dla jednej pamięci podręcznej. Pamięć podręczna przedstawia wszystkie cele magazynu w jednej zagregowanej przestrzeni nazw.

Ścieżki przestrzeni nazw są konfigurowane oddzielnie po dodaniu miejsc docelowych magazynu. Ogólnie rzecz biorąc, miejsce docelowe magazynu NFS może mieć do dziesięciu ścieżek przestrzeni nazw lub wiele wielu dużych konfiguracji. Aby uzyskać szczegółowe informacje, Przeczytaj [ścieżki przestrzeni nazw systemu plików NFS](add-namespace-paths.md#nfs-namespace-paths) .

Należy pamiętać, że eksporty magazynu muszą być dostępne z sieci wirtualnej pamięci podręcznej. W przypadku lokalnego magazynu sprzętu może być konieczne skonfigurowanie serwera DNS, który może rozpoznawać nazwy hostów dla dostępu do magazynu NFS. Więcej informacji można znaleźć w temacie [dostęp do usługi DNS](hpc-cache-prerequisites.md#dns-access).

Dodaj elementy docelowe magazynu po utworzeniu pamięci podręcznej. Wykonaj następujące czynności:

1. [Tworzenie pamięci podręcznej](hpc-cache-create.md)
1. Zdefiniuj miejsce docelowe magazynu (informacje w tym artykule)
1. [Tworzenie ścieżek związanych z klientem](add-namespace-paths.md) (dla [zagregowanej przestrzeni nazw](hpc-cache-namespace.md))

Procedura dodawania miejsca docelowego magazynu jest nieco inna w zależności od używanego typu magazynu. Poniżej znajdują się szczegółowe informacje dotyczące każdego z nich.

Kliknij poniższy obraz, aby obejrzeć [film wideo](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) przedstawiający tworzenie pamięci podręcznej i Dodawanie miejsca docelowego magazynu z Azure Portal.

[![Miniatura wideo: Azure HPC cache: Setup (kliknij, aby odwiedzić stronę wideo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Dodaj nowy element docelowy usługi Azure Blob Storage

Nowy obiekt docelowy magazynu obiektów BLOB wymaga pustego kontenera obiektów blob lub kontenera, który został wypełniony danymi w formacie systemu plików w chmurze usługi Azure HPC. Przeczytaj więcej na temat wstępnego ładowania kontenera obiektów BLOB w temacie [przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md).

Strona Azure Portal **dodawania miejsca docelowego magazynu** zawiera opcję utworzenia nowego kontenera obiektów BLOB przed dodaniem go.

> [!NOTE]
> W przypadku magazynu obiektów BLOB zainstalowanego w systemie plików NFS należy użyć typu [docelowego magazynu ADLS-NFS](#) .

### <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Portal Otwórz wystąpienie pamięci podręcznej, a następnie kliknij pozycję **cele magazynu** na lewym pasku bocznym.

![zrzut ekranu przedstawiający stronę Ustawienia > miejsce docelowe magazynu z dwoma istniejącymi obiektami docelowymi magazynu w tabeli oraz wyróżnieniem wokół przycisku + Dodaj miejsce docelowe magazynu powyżej tabeli](media/add-storage-target-button.png)

Na stronie **miejsce docelowe magazynu** są wyświetlane wszystkie istniejące elementy docelowe i nadające się do dodania łącze.

Kliknij przycisk **Dodaj cel magazynu** .

![zrzut ekranu przedstawiający stronę Dodawanie miejsca docelowego magazynu, wypełniony informacjami o nowym obiekcie docelowym usługi Azure Blob Storage](media/hpc-cache-add-blob.png)

Aby zdefiniować kontener obiektów blob platformy Azure, wprowadź te informacje.

* **Nazwa docelowego magazynu** — Ustaw nazwę identyfikującą ten element docelowy magazynu w pamięci podręcznej platformy Azure HPC.
* **Typ docelowy** — wybierz **obiekt BLOB**.
* **Konto magazynu** — wybierz konto, którego chcesz użyć.

  Musisz autoryzować wystąpienie pamięci podręcznej, aby uzyskać dostęp do konta magazynu, zgodnie z opisem w temacie [Dodawanie ról dostępu](#add-the-access-control-roles-to-your-account).

  Aby uzyskać informacje o rodzaju konta magazynu, którego można użyć, przeczytaj temat [wymagania dotyczące usługi BLOB Storage](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Kontener magazynu** — wybierz kontener obiektów BLOB dla tego obiektu docelowego lub kliknij pozycję **Utwórz nowy**.

  ![zrzut ekranu przedstawiający okno dialogowe, w którym można określić poziom nazw i dostępu (prywatny) dla nowego kontenera](media/add-blob-new-container.png)

Po zakończeniu kliknij przycisk **OK** , aby dodać miejsce docelowe magazynu.

> [!NOTE]
> Jeśli Zapora konta magazynu jest ustawiona w taki sposób, aby ograniczyć dostęp tylko do wybranych sieci, użyj tymczasowego obejścia udokumentowanego w obszarze [Pracuj wokół ustawień zapory konta magazynu obiektów BLOB](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Dodaj role kontroli dostępu do swojego konta

Pamięć podręczna Azure HPC używa [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/index.yml) w celu autoryzowania usługi pamięci podręcznej do uzyskiwania dostępu do konta magazynu dla obiektów docelowych usługi Azure Blob Storage.

Właściciel konta magazynu musi jawnie dodać rolę współautor danych [współautor konta magazynu](../role-based-access-control/built-in-roles.md#storage-account-contributor) i [obiektu blob magazynu](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) dla użytkownika "dostawca zasobów pamięci podręcznej usługi HPC".

Można to zrobić wcześniej lub przez kliknięcie linku na stronie, w którym można dodać obiekt docelowy magazynu obiektów BLOB. Należy pamiętać, że może upłynąć do 5 minut, zanim ustawienia roli są propagowane za pomocą środowiska platformy Azure, więc przed utworzeniem elementu docelowego magazynu należy poczekać kilka minut.

Kroki umożliwiające dodanie ról platformy Azure:

1. Otwórz stronę **kontroli dostępu (IAM)** dla konta magazynu. (Link na stronie **Dodaj miejsce docelowe magazynu** automatycznie otwiera Tę stronę dla wybranego konta).

1. Kliknij w **+** górnej części strony i wybierz polecenie **Dodaj przypisanie roli**.

1. Wybierz z listy rolę "Współautor konta magazynu".

1. W polu **Przypisz dostęp do** pozostaw wartość domyślną wybrana ("użytkownik, Grupa lub nazwa główna usługi").  

1. W polu **Wybierz** Wyszukaj ciąg "HPC".  Ten ciąg powinien być zgodny z jedną jednostką usługi o nazwie "dostawca zasobów pamięci podręcznej HPC". Kliknij ten podmiot zabezpieczeń, aby go wybrać.

   > [!NOTE]
   > Jeśli wyszukiwanie "HPC" nie działa, spróbuj użyć zamiast niego ciągu "storagecache". Użytkownicy korzystający z wersji zapoznawczych (przed uzyskaniem wiedzy) mogą wymagać użycia starszej nazwy dla jednostki usługi.

1. Kliknij przycisk **Zapisz** u dołu.

1. Powtórz ten proces, aby przypisać rolę "Współautor danych obiektu blob magazynu".  

![zrzut ekranu przedstawiający graficzny interfejs użytkownika dodawania przypisania roli](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Wymaganie wstępne: dostęp do konta magazynu

[Konfigurowanie interfejsu wiersza polecenia platformy Azure dla pamięci podręcznej platformy Azure HPC](./az-cli-prerequisites.md).

Przed dodaniem celu magazynu obiektów BLOB należy sprawdzić, czy pamięć podręczna ma odpowiednie role umożliwiające uzyskanie dostępu do konta magazynu, a ustawienia zapory umożliwiają tworzenie miejsca docelowego magazynu.

Pamięć podręczna Azure HPC używa [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/index.yml) w celu autoryzowania usługi pamięci podręcznej do uzyskiwania dostępu do konta magazynu dla obiektów docelowych usługi Azure Blob Storage.

Właściciel konta magazynu musi jawnie dodać rolę współautor danych [współautor konta magazynu](../role-based-access-control/built-in-roles.md#storage-account-contributor) i [obiektu blob magazynu](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) dla użytkownika "dostawca zasobów pamięci podręcznej usługi HPC".

Tworzenie miejsca docelowego magazynu zakończy się niepowodzeniem, jeśli pamięć podręczna nie ma tych ról.

Propagacja ustawień roli przez środowisko platformy Azure może potrwać do 5 minut, więc należy poczekać kilka minut po dodaniu ról przed utworzeniem miejsca docelowego magazynu.

Szczegółowe instrukcje można znaleźć w artykule [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md) .

Sprawdź również ustawienia zapory dla konta magazynu. Jeśli Zapora jest ustawiona w taki sposób, aby ograniczyć dostęp tylko do wybranych sieci, Tworzenie miejsca docelowego magazynu może zakończyć się niepowodzeniem. Skorzystaj z obejścia opisanego w obszarze [Pracuj wokół ustawień zapory konta magazynu obiektów BLOB](hpc-cache-blob-firewall-fix.md).

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Dodawanie docelowego magazynu obiektów BLOB przy użyciu interfejsu wiersza polecenia platformy Azure

Aby zdefiniować obiekt docelowy usługi Azure Blob Storage, użyj [AZ HPC-cache BLOB-Storage-Target Add](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) Interface.

> [!NOTE]
> Polecenie interfejsu wiersza polecenia platformy Azure wymaga obecnie utworzenia ścieżki przestrzeni nazw podczas dodawania miejsca docelowego magazynu. Różni się to od procesu używanego z interfejsem Azure Portal.

Oprócz standardowej grupy zasobów i parametrów nazwy pamięci podręcznej, należy podać następujące opcje dla miejsca docelowego magazynu:

* ``--name`` -Ustaw nazwę identyfikującą ten element docelowy magazynu w pamięci podręcznej platformy Azure HPC.

* ``--storage-account`` — Identyfikator konta w tej formie:/subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/Providers/Microsoft.Storage/storageAccounts/*<account_name>*

  Aby uzyskać informacje o rodzaju konta magazynu, którego można użyć, przeczytaj temat [wymagania dotyczące usługi BLOB Storage](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name`` -Określ nazwę kontenera, który ma być używany dla tego miejsca docelowego magazynu.

* ``--virtual-namespace-path`` -Ustaw ścieżkę pliku dla klienta dla tego miejsca docelowego magazynu. Ujmij ścieżki w znaki cudzysłowu. Przeczytaj temat [Planowanie zagregowanej przestrzeni nazw,](hpc-cache-namespace.md) aby dowiedzieć się więcej o funkcji wirtualnej przestrzeni nazw.

Przykładowe polecenie:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Dodaj nowy element docelowy magazynu NFS

Obiekt docelowy magazynu NFS ma inne ustawienia niż obiekt docelowy magazynu obiektów BLOB. Ustawienie model użycia pomaga pamięci podręcznej w celu wydajnej pamięci podręcznej danych z tego systemu magazynu.

![Zrzut ekranu przedstawiający stronę Dodawanie miejsca docelowego magazynu z zdefiniowanym elementem docelowym NFS](media/add-nfs-target.png)

> [!NOTE]
> Przed utworzeniem miejsca docelowego magazynu NFS upewnij się, że system magazynu jest dostępny z pamięci podręcznej platformy Azure HPC i spełnia wymagania dotyczące uprawnień. Tworzenie docelowego magazynu zakończy się niepowodzeniem, jeśli pamięć podręczna nie będzie mogła uzyskać dostępu do systemu magazynu. Aby uzyskać szczegółowe informacje, zapoznaj się z [wymaganiami dotyczącymi magazynu NFS](hpc-cache-prerequisites.md#nfs-storage-requirements) i [Rozwiązywanie problemów z usługą konfiguracja serwera nas i docelowymi](troubleshoot-nas.md)

### <a name="choose-a-usage-model"></a>Wybierz model użycia
<!-- referenced from GUI by aka.ms link -->

Podczas tworzenia miejsca docelowego magazynu korzystającego z systemu plików NFS w celu uzyskania połączenia z systemem magazynu należy wybrać model użycia dla tego celu. Ten model określa, w jaki sposób dane są buforowane.

Więcej informacji o tych ustawieniach można znaleźć w [opisie modeli użycia](cache-usage-models.md) .

Wbudowane modele użycia umożliwiają wybranie sposobu zrównoważenia szybkiego reagowania na ryzyko związane z uzyskaniem starych danych. Jeśli chcesz zoptymalizować szybkość odczytywania plików, możesz nie zadbać o to, czy pliki w pamięci podręcznej są sprawdzane względem plików zaplecza. Z drugiej strony, jeśli chcesz upewnić się, że pliki są zawsze aktualne z magazynem zdalnym, wybierz model, który sprawdza się często.

Te trzy opcje obejmują większość sytuacji:

* **Czytaj duże, rzadko** występujące zapisy — przyspiesza dostęp do odczytu do plików, które są statyczne lub rzadko zmieniane.

  Ta opcja powoduje buforowanie plików z odczytów klienta, ale natychmiast przekazuje dane z klienta do magazynu zaplecza. Pliki przechowywane w pamięci podręcznej nie są automatycznie porównywane z plikami znajdującymi się na woluminie magazynu NFS.

  Nie należy używać tej opcji, jeśli istnieje ryzyko, że plik może być modyfikowany bezpośrednio w systemie magazynu bez wcześniejszego zapisania go w pamięci podręcznej. W takim przypadku buforowana wersja pliku nie jest zsynchronizowana z plikiem zaplecza.

* **Więcej niż 15% zapisów** — ta opcja przyspiesza zarówno wydajność odczytu, jak i zapisu.

  Operacje odczytu i zapisu klienta są buforowane. Pliki w pamięci podręcznej są zakładane jako nowsze niż pliki w systemie magazynu zaplecza. Buforowane pliki są automatycznie sprawdzane względem plików w magazynie zaplecza co osiem godzin. Zmodyfikowane pliki w pamięci podręcznej są zapisywane w systemie magazynu zaplecza, gdy znajdowały się w pamięci podręcznej przez 20 minut bez dodatkowych zmian.

  Nie należy używać tej opcji, jeśli jakikolwiek klient zainstaluje wolumin magazynu zaplecza bezpośrednio, ponieważ istnieje ryzyko związane z nieaktualnymi plikami.

* **Klienci zapisują w miejscu DOCELOWYM NFS, pomijając pamięć podręczną** — wybierz tę opcję, jeśli dowolni klienci w przepływie pracy zapisują dane bezpośrednio w systemie magazynu bez wcześniejszego zapisu w pamięci podręcznej lub jeśli chcesz zoptymalizować spójność danych.

  Pliki, które są przechowywane w pamięci podręcznej żądań klientów, ale wszelkie zmiany tych plików z klienta są natychmiast przesyłane do systemu magazynu zaplecza. Pliki w pamięci podręcznej są często sprawdzane względem wersji zaplecza dla aktualizacji. Ta weryfikacja zachowuje spójność danych, gdy pliki są zmieniane bezpośrednio w systemie magazynu, a nie za pomocą pamięci podręcznej.

Aby uzyskać szczegółowe informacje na temat innych opcji, Przeczytaj [Opis modeli użycia](cache-usage-models.md).

Ta tabela zawiera podsumowanie różnic między wszystkimi modelami użycia:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> Wartość **weryfikacyjna zaplecza** jest wyświetlana, gdy pamięć podręczna porównuje pliki z plikami źródłowymi w magazynie zdalnym. Można jednak wyzwolić porównanie, wysyłając żądanie klienta zawierające operację READDIRPLUS w systemie magazynu zaplecza. READDIRPLUS to standardowy interfejs API systemu plików NFS (nazywany także rozszerzonym odczytem), który zwraca metadane katalogu, co powoduje, że pamięć podręczna będzie porównywać i aktualizować pliki.

### <a name="create-an-nfs-storage-target"></a>Tworzenie miejsca docelowego magazynu NFS

### <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Portal Otwórz wystąpienie pamięci podręcznej, a następnie kliknij pozycję **cele magazynu** na lewym pasku bocznym.

![zrzut ekranu przedstawiający stronę Ustawienia > miejsce docelowe magazynu z dwoma istniejącymi obiektami docelowymi magazynu w tabeli oraz wyróżnieniem wokół przycisku + Dodaj miejsce docelowe magazynu powyżej tabeli](media/add-storage-target-button.png)

Na stronie **miejsce docelowe magazynu** są wyświetlane wszystkie istniejące elementy docelowe i nadające się do dodania łącze.

Kliknij przycisk **Dodaj cel magazynu** .

![Zrzut ekranu przedstawiający stronę Dodawanie miejsca docelowego magazynu z zdefiniowanym elementem docelowym NFS](media/add-nfs-target.png)

Podaj te informacje dla miejsca docelowego magazynu z kopią zapasową NFS:

* **Nazwa docelowego magazynu** — Ustaw nazwę identyfikującą ten element docelowy magazynu w pamięci podręcznej platformy Azure HPC.

* **Typ docelowy** — wybierz system **plików NFS**.

* **Nazwa hosta** — wprowadź adres IP lub w pełni kwalifikowaną nazwę domeny dla systemu magazynu NFS. (Użyj nazwy domeny tylko wtedy, gdy pamięć podręczna ma dostęp do serwera DNS, który może rozpoznać nazwę).

* **Model użycia** — wybierz jeden z profili buforowania danych opartych na przepływie pracy, zgodnie z opisem w temacie [Wybierz model użycia](#choose-a-usage-model) powyżej.

Po zakończeniu kliknij przycisk **OK** , aby dodać miejsce docelowe magazynu.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Konfigurowanie interfejsu wiersza polecenia platformy Azure dla pamięci podręcznej platformy Azure HPC](./az-cli-prerequisites.md).

Użycie interfejsu wiersza polecenia platformy Azure [AZ HPC-cache NFS-Storage-Target Add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) w celu utworzenia miejsca docelowego magazynu.

> [!NOTE]
> Polecenie interfejsu wiersza polecenia platformy Azure wymaga obecnie utworzenia ścieżki przestrzeni nazw podczas dodawania miejsca docelowego magazynu. Różni się to od procesu używanego z interfejsem Azure Portal.

Podaj te wartości oprócz nazwy pamięci podręcznej i grupy zasobów pamięci podręcznej:

* ``--name`` -Ustaw nazwę identyfikującą ten element docelowy magazynu w pamięci podręcznej platformy Azure HPC.
* ``--nfs3-target`` — Adres IP systemu magazynu NFS. (W tym miejscu możesz użyć w pełni kwalifikowanej nazwy domeny, jeśli pamięć podręczna ma dostęp do serwera DNS, który może rozpoznać nazwę).
* ``--nfs3-usage-model`` -Jeden z profili buforowania danych opisany w temacie [Wybierz model użycia](#choose-a-usage-model)powyżej.

  Sprawdź nazwy modeli użycia za pomocą polecenia [AZ HPC-cache-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

* ``--junction`` -Parametr połączenia łączy ścieżkę pliku wirtualnego po stronie klienta ze ścieżką eksportu w systemie magazynu.

  Obiekt docelowy magazynu NFS może mieć wiele ścieżek wirtualnych, o ile każda ścieżka reprezentuje inny eksport lub podkatalog w tym samym systemie magazynu. Utwórz wszystkie ścieżki dla jednego systemu magazynu w jednym miejscu docelowym magazynu.

  [Ścieżki przestrzeni nazw można dodawać i edytować](add-namespace-paths.md) w miejscu docelowym magazynu w dowolnym momencie.

  ``--junction``Parametr używa tych wartości:

  * ``namespace-path`` -Ścieżka pliku wirtualnego po stronie klienta
  * ``nfs-export`` — Eksport systemu magazynu w celu skojarzenia ze ścieżką skierowaną do klienta
  * ``target-path`` (opcjonalnie) — podkatalog eksportu, w razie konieczności

  Przykład: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Przeczytaj temat [Konfigurowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md) , aby dowiedzieć się więcej o funkcji wirtualnej przestrzeni nazw.

Przykładowe polecenie:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Dane wyjściowe:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target-preview"></a>Dodawanie nowego miejsca docelowego magazynu ADLS-NFS (wersja zapoznawcza)

Cele magazynu ADLS-NFS używają kontenerów obiektów blob platformy Azure, które obsługują protokół Network File System (NFS) 3,0.

> [!NOTE]
> Obsługa protokołu NFS 3,0 dla usługi Azure Blob Storage jest w publicznej wersji zapoznawczej. Dostępność jest ograniczona, a funkcje mogą ulec zmianie między teraz i gdy funkcja będzie ogólnie dostępna. Nie używaj technologii wersji zapoznawczej w systemach produkcyjnych.
>
> Zapoznaj się z [obsługą protokołu NFS 3,0](../storage/blobs/network-file-system-protocol-support.md) , aby uzyskać najnowsze informacje.

Cele magazynu ADLS-NFS mają różne podobieństwa z obiektami docelowymi magazynu obiektów blob, a niektóre z nich są obiektami docelowymi magazynu NFS. Na przykład:

* Podobnie jak w przypadku celu usługi BLOB Storage, należy nadać uprawnienia pamięci podręcznej platformy Azure HPC do [uzyskiwania dostępu do konta magazynu](#add-the-access-control-roles-to-your-account).
* Podobnie jak w przypadku miejsca docelowego magazynu NFS, należy ustawić [model użycia](#choose-a-usage-model)pamięci podręcznej.
* Ponieważ kontenery obiektów BLOB obsługujące system plików NFS mają strukturę hierarchiczną zgodną z systemem plików NFS, nie trzeba używać pamięci podręcznej do pozyskiwania danych, a kontenery są odczytywane przez inne systemy plików NFS. Można wstępnie załadować dane w kontenerze ADLS-NFS, a następnie dodać je do pamięci podręcznej HPC jako miejsce docelowe magazynu, a następnie uzyskać dostęp do danych później spoza pamięci podręcznej HPC. W przypadku używania standardowego kontenera obiektów BLOB jako miejsca docelowego magazynu pamięci podręcznej HPC dane są zapisywane w formacie zastrzeżonym i dostępne są tylko z innych produktów zgodnych z pamięcią podręczną platformy Azure HPC.

Aby można było utworzyć obiekt docelowy magazynu ADLS-NFS, należy utworzyć konto magazynu z obsługą systemu plików NFS. Postępuj zgodnie ze wskazówkami w sekcji [wymagania wstępne dotyczące usługi Azure HPC cache](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) i instrukcje w temacie [Instalowanie magazynu obiektów BLOB przy użyciu systemu plików NFS](../storage/blobs/network-file-system-protocol-support-how-to.md). Po skonfigurowaniu konta magazynu można utworzyć nowy kontener podczas tworzenia miejsca docelowego magazynu.

Aby dowiedzieć się więcej na temat tej konfiguracji, przeczytaj artykuł [Używanie magazynu obiektów BLOB zainstalowanego w systemie plików NFS z pamięcią podręczną Azure HPC](nfs-blob-considerations.md)

Aby utworzyć obiekt docelowy magazynu ADLS-NFS, Otwórz stronę **Dodawanie miejsca docelowego magazynu** w Azure Portal. (Dodatkowe metody są w trakcie tworzenia).

![Zrzut ekranu przedstawiający stronę Dodawanie miejsca docelowego magazynu z zdefiniowanym elementem docelowym ADLS-NFS](media/add-adls-target.png)

Wprowadź te informacje.

* **Nazwa docelowego magazynu** — Ustaw nazwę identyfikującą ten element docelowy magazynu w pamięci podręcznej platformy Azure HPC.
* **Typ docelowy** — wybierz pozycję **ADLS-NFS**.
* **Konto magazynu** — wybierz konto, którego chcesz użyć. Jeśli na liście nie ma konta magazynu obsługującego system plików NFS, sprawdź, czy jest ono zgodne z wymaganiami wstępnymi i czy pamięć podręczna ma do niej dostęp.

  Musisz autoryzować wystąpienie pamięci podręcznej, aby uzyskać dostęp do konta magazynu, zgodnie z opisem w temacie [Dodawanie ról dostępu](#add-the-access-control-roles-to-your-account).

* **Kontener magazynu** — wybierz kontener obiektów blob z obsługą systemu plików NFS dla tego obiektu docelowego lub kliknij pozycję **Utwórz nowy**.

* **Model użycia** — wybierz jeden z profili buforowania danych opartych na przepływie pracy, zgodnie z opisem w temacie [Wybierz model użycia](#choose-a-usage-model) powyżej.

Po zakończeniu kliknij przycisk **OK** , aby dodać miejsce docelowe magazynu.

## <a name="view-storage-targets"></a>Wyświetl cele magazynu

Możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby pokazać cele magazynu zdefiniowane już dla pamięci podręcznej.

### <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Portal Otwórz wystąpienie pamięci podręcznej, a następnie kliknij pozycję **cele magazynu**, która znajduje się poniżej nagłówka ustawienia na lewym pasku bocznym. Na stronie miejsce docelowe magazynu są wyświetlane wszystkie istniejące elementy docelowe i kontrolki umożliwiające ich dodanie lub usunięcie.

Kliknij nazwę docelowego magazynu, aby otworzyć jego stronę szczegółów.

Aby dowiedzieć się więcej, przeczytaj artykuł [Edycja miejsc docelowych](hpc-cache-edit-storage.md) .

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Konfigurowanie interfejsu wiersza polecenia platformy Azure dla pamięci podręcznej platformy Azure HPC](./az-cli-prerequisites.md).

Użyj opcji [AZ HPC-cache Storage-Target](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) , aby wyświetlić istniejące cele magazynu dla pamięci podręcznej. Podaj nazwę pamięci podręcznej i grupę zasobów (chyba że została ustawiona globalnie).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Użyj [AZ HPC-cache Storage-Target show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) , aby wyświetlić szczegóły dotyczące określonego miejsca docelowego magazynu. (Określ miejsce docelowe magazynu według nazwy).

Przykład:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obiektów docelowych magazynu wykonaj te zadania w celu przygotowania pamięci podręcznej do użycia:

* [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md)
* [Instalowanie pamięci podręcznej usługi Azure HPC Cache](hpc-cache-mount.md)
* [Przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md)

Jeśli trzeba zaktualizować dowolne ustawienia, można [edytować miejsce docelowe magazynu](hpc-cache-edit-storage.md).

---
title: Dodawanie magazynu do Azure HPC Cache
description: Jak zdefiniować obiekty docelowe magazynu, aby Azure HPC Cache używać lokalnego systemu plików NFS lub kontenerów obiektów blob platformy Azure na użytek długoterminowego magazynu plików
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 708ad8bbfec9e3fd0176c53c111b5b5b25a5318f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862241"
---
# <a name="add-storage-targets"></a>Dodawanie lokalizacji docelowych magazynu

*Obiekty docelowe* magazynu to magazyny w zadomowiu dla plików, do których uzyskuje się dostęp za pośrednictwem Azure HPC Cache. Możesz dodać magazyn NFS (na przykład lokalnego systemu sprzętowego) lub przechowywać dane w usłudze Azure Blob.

Dla jednej pamięci podręcznej można zdefiniować maksymalnie 20 różnych obiektów docelowych magazynu. Pamięć podręczna przedstawia wszystkie obiekty docelowe magazynu w jednej zagregowanej przestrzeni nazw.

Ścieżki przestrzeni nazw są konfigurowane oddzielnie po dodaniu obiektów docelowych magazynu. Ogólnie rzecz biorąc, miejsce docelowe magazynu NFS może mieć maksymalnie dziesięć ścieżek przestrzeni nazw lub więcej w przypadku niektórych dużych konfiguracji. Aby uzyskać szczegółowe informacje, przeczytaj ścieżki przestrzeni nazw systemu plików [NFS.](add-namespace-paths.md#nfs-namespace-paths)

Należy pamiętać, że eksporty magazynu muszą być dostępne z sieci wirtualnej pamięci podręcznej. W przypadku lokalnego magazynu sprzętowego może być konieczne skonfigurowanie serwera DNS, który może rozpoznać nazwy hostów na potrzeby dostępu do magazynu NFS. Przeczytaj więcej na temat [dostępu DNS.](hpc-cache-prerequisites.md#dns-access)

Dodaj obiekty docelowe magazynu po utworzeniu pamięci podręcznej. Wykonaj ten proces:

1. [Tworzenie pamięci podręcznej](hpc-cache-create.md)
1. Definiowanie miejsca docelowego magazynu (informacje w tym artykule)
1. [Tworzenie ścieżek skierowanych do klienta](add-namespace-paths.md) (dla [zagregowanej przestrzeni nazw](hpc-cache-namespace.md))

Procedura dodawania miejsca docelowego magazynu różni się nieco w zależności od typu używanego magazynu. Szczegóły każdego z nich znajdują się poniżej.

Kliknij poniższy obraz, aby obejrzeć [pokaz wideo](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) z tworzeniem pamięci podręcznej i dodawaniem miejsca docelowego magazynu z Azure Portal.

[![miniatura wideo: Azure HPC Cache: Instalator (kliknij, aby odwiedzić stronę wideo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Dodawanie nowego obiektu docelowego usługi Azure Blob Storage

Nowy obiekt docelowy usługi Blob Storage wymaga pustego kontenera obiektów blob lub kontenera wypełnionego danymi w Azure HPC Cache formatu systemu plików w chmurze. Dowiedz się więcej na temat wstępnego ładowania kontenera obiektów blob w [tesłudze Move data to Azure Blob Storage (Przenoszenie danych do usługi Azure Blob Storage).](hpc-cache-ingest.md)

Strona Azure Portal **Dodawanie miejsca docelowego** magazynu zawiera opcję utworzenia nowego kontenera obiektów blob tuż przed dodaniem go.

> [!NOTE]
> W przypadku magazynu obiektów blob zainstalowanego z użyciem systemu plików NFS użyj typu docelowego magazynu [ADLS-NFS.](#)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Z poziomu Azure Portal pamięci podręcznej otwórz wystąpienie pamięci podręcznej i kliknij pozycję **Miejsca docelowe** magazynu na lewym pasku bocznym.

![zrzut ekranu przedstawiający > docelowego magazynu z dwoma istniejącymi obiektami docelowymi magazynu w tabeli i wyróżnieniem wokół przycisku + Dodaj miejsce docelowe magazynu nad tabelą](media/add-storage-target-button.png)

Strona **Miejsca docelowe** magazynu zawiera listę wszystkich istniejących obiektów docelowych i udostępnia link do dodawania nowego.

Kliknij przycisk **Add storage target (Dodaj miejsce docelowe** magazynu).

![zrzut ekranu przedstawiający stronę dodawania miejsca docelowego magazynu wypełnioną informacjami o nowym obiekcie docelowym usługi Azure Blob Storage](media/hpc-cache-add-blob.png)

Aby zdefiniować kontener obiektów blob platformy Azure, wprowadź te informacje.

* **Nazwa miejsca docelowego** magazynu — ustaw nazwę identyfikującą ten obiekt docelowy magazynu w Azure HPC Cache.
* **Typ docelowy** — wybierz pozycję **Obiekt blob.**
* **Konto magazynu** — wybierz konto, którego chcesz użyć.

  Należy autoryzować wystąpienie pamięci podręcznej w celu uzyskania dostępu do konta magazynu zgodnie z opisem w tece [Dodawanie ról dostępu.](#add-the-access-control-roles-to-your-account)

  Aby uzyskać informacje na temat rodzaju konta magazynu, z których można korzystać, zapoznaj się z wymaganiami [usługi Blob Storage.](hpc-cache-prerequisites.md#blob-storage-requirements)

* **Kontener magazynu** — wybierz kontener obiektów blob dla tego obiektu docelowego lub kliknij **pozycję Utwórz nowy**.

  ![zrzut ekranu przedstawiający okno dialogowe z określeniem nazwy i poziomu dostępu (prywatnego) dla nowego kontenera](media/add-blob-new-container.png)

Po zakończeniu kliknij przycisk **OK,** aby dodać miejsce docelowe magazynu.

> [!NOTE]
> Jeśli zapora konta magazynu jest ustawiona w celu ograniczenia dostępu tylko do "wybranych sieci", użyj tymczasowego obejścia opisanego w te tematze Obejście ustawień zapory [konta usługi Blob Storage.](hpc-cache-blob-firewall-fix.md)

### <a name="add-the-access-control-roles-to-your-account"></a>Dodawanie ról kontroli dostępu do konta

Azure HPC Cache używa kontroli dostępu opartej na rolach [(RBAC)](../role-based-access-control/index.yml) platformy Azure, aby autoryzować usługę pamięci podręcznej do uzyskiwania dostępu do konta magazynu dla obiektów docelowych usługi Azure Blob Storage.

Właściciel konta magazynu musi jawnie dodać role Współautor konta magazynu i Współautor danych obiektu [blob](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) magazynu dla użytkownika "HPC Cache dostawcy zasobów". [](../role-based-access-control/built-in-roles.md#storage-account-contributor)

Możesz to zrobić wcześniej lub klikając link na stronie, na której dodajesz obiekt docelowy usługi Blob Storage. Pamiętaj, że propagacja ustawień roli w środowisku platformy Azure może potrwać do pięciu minut, dlatego przed utworzeniem obiektu docelowego magazynu należy odczekać kilka minut po dodaniu ról.

Kroki dodawania ról platformy Azure:

1. Otwórz stronę **Kontrola dostępu (IAM)** dla konta magazynu. (Link na stronie **Dodawanie miejsca docelowego** magazynu automatycznie otwiera tę stronę dla wybranego konta).

1. Kliknij **+** ikonę w górnej części strony i wybierz pozycję **Dodaj przypisanie roli.**

1. Wybierz rolę "Współautor konta magazynu" z listy.

1. W polu **Przypisz dostęp do** pozostaw wybraną wartość domyślną ("Użytkownik, grupa lub nazwa główna usługi Azure AD").  

1. W polu **Wybierz** wyszukaj pozycję "hpc".  Ten ciąg powinien odpowiadać jednej nazwie głównej usługi o nazwie "HPC Cache dostawcy zasobów". Kliknij tę jednostkę, aby ją wybrać.

   > [!NOTE]
   > Jeśli wyszukiwanie "hpc" nie działa, spróbuj zamiast tego użyć ciągu "storagecache". Użytkownicy, którzy uczestniczyli w wersjach zapoznawczych (przed rozpoczęciem korzystania z wersji zapoznawczych), mogą potrzebować starszej nazwy jednostki usługi.

1. Kliknij przycisk **Zapisz** u dołu.

1. Powtórz ten proces, aby przypisać rolę "Współautor danych obiektu blob magazynu".  

![zrzut ekranu przedstawiający graficzny interfejs użytkownika dodawania przypisania roli](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Wymaganie wstępne: Dostęp do konta magazynu

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

Przed dodaniem obiektu docelowego magazynu obiektów blob sprawdź, czy pamięć podręczna ma poprawne role umożliwiające dostęp do konta magazynu, a ustawienia zapory umożliwią utworzenie miejsca docelowego magazynu.

Azure HPC Cache używa kontroli dostępu na podstawie ról [(RBAC)](../role-based-access-control/index.yml) platformy Azure, aby autoryzować usługę pamięci podręcznej do uzyskiwania dostępu do konta magazynu dla obiektów docelowych usługi Azure Blob Storage.

Właściciel konta magazynu musi jawnie dodać role Współautor konta magazynu i Współautor danych obiektu [blob](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) magazynu dla użytkownika "HPC Cache zasobów". [](../role-based-access-control/built-in-roles.md#storage-account-contributor)

Tworzenie miejsca docelowego magazynu nie powiedzie się, jeśli pamięć podręczna nie ma tych ról.

Propagacja ustawień roli w środowisku platformy Azure może potrwać do pięciu minut, dlatego należy poczekać kilka minut po dodaniu ról przed utworzeniem obiektu docelowego magazynu.

Aby uzyskać szczegółowe instrukcje, zobacz Dodawanie lub usuwanie [przypisań ról platformy Azure przy](../role-based-access-control/role-assignments-cli.md) użyciu interfejsu wiersza polecenia platformy Azure.

Sprawdź również ustawienia zapory konta magazynu. Jeśli zapora jest ustawiona w celu ograniczenia dostępu tylko do "wybranych sieci", tworzenie miejsca docelowego magazynu może się nie powieść. Skorzystaj z obejścia opisanego w te [tematze Obejście](hpc-cache-blob-firewall-fix.md)ustawień zapory konta usługi Blob Storage.

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Dodawanie obiektu docelowego magazynu obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure

Użyj [interfejsu az hpc-cache blob-storage-target add,](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add) aby zdefiniować obiekt docelowy usługi Azure Blob Storage.

> [!NOTE]
> Polecenia interfejsu wiersza polecenia platformy Azure wymagają obecnie utworzenia ścieżki przestrzeni nazw podczas dodawania miejsca docelowego magazynu. Różni się to od procesu używanego z interfejsem Azure Portal interfejsu.

Oprócz standardowych parametrów nazwy grupy zasobów i pamięci podręcznej należy podać następujące opcje dla obiektu docelowego magazynu:

* ``--name`` - Ustaw nazwę identyfikującą ten obiekt docelowy magazynu w Azure HPC Cache.

* ``--storage-account`` - Identyfikator konta w tej postaci: /subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAccounts/*<account_name>*

  Aby uzyskać informacje na temat rodzaju konta magazynu, z których można korzystać, zapoznaj się z wymaganiami [usługi Blob Storage.](hpc-cache-prerequisites.md#blob-storage-requirements)

* ``--container-name`` - Określ nazwę kontenera do użycia dla tego obiektu docelowego magazynu.

* ``--virtual-namespace-path`` — ustaw ścieżkę pliku dla klienta dla tego obiektu docelowego magazynu. Ująć ścieżki w cudzysłów. Przeczytaj [temat Planowanie zagregowanej przestrzeni nazw,](hpc-cache-namespace.md) aby dowiedzieć się więcej o funkcji wirtualnej przestrzeni nazw.

Przykładowe polecenie:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Dodawanie nowego obiektu docelowego magazynu NFS

Obiekt docelowy magazynu NFS ma inne ustawienia niż obiekt docelowy magazynu obiektów blob. Ustawienie modelu użycia pomaga pamięci podręcznej wydajnie buforować dane z tego systemu magazynowania.

![Zrzut ekranu przedstawiający stronę dodawania miejsca docelowego magazynu ze zdefiniowanym obiektem docelowym systemu plików NFS](media/add-nfs-target.png)

> [!NOTE]
> Przed utworzeniem obiektu docelowego magazynu NFS upewnij się, że system magazynu jest dostępny z poziomu Azure HPC Cache i spełnia wymagania dotyczące uprawnień. Tworzenie miejsca docelowego magazynu nie powiedzie się, jeśli pamięć podręczna nie będzie mieć dostępu do systemu magazynu. Aby uzyskać szczegółowe informacje, przeczytaj [wymagania dotyczące magazynu NFS](hpc-cache-prerequisites.md#nfs-storage-requirements) i Rozwiązywanie problemów z konfiguracją serwera NAS i obiektem docelowym magazynu [NFS.](troubleshoot-nas.md)

### <a name="choose-a-usage-model"></a>Wybieranie modelu użycia
<!-- referenced from GUI by aka.ms link -->

Podczas tworzenia obiektu docelowego magazynu, który używa systemu plików NFS do osiągnięcia systemu magazynowania, należy wybrać model użycia dla tego obiektu docelowego. Ten model określa sposób buforowania danych.

Aby uzyskać więcej informacji na temat wszystkich tych ustawień, zobacz Understand [usage models](cache-usage-models.md) (Informacje o modelach użycia).

Wbudowane modele użycia pozwalają wybrać sposób równoważenia szybkiej reakcji z ryzykiem uzyskania nieaktualnych danych. Jeśli chcesz zoptymalizować szybkość odczytywania plików, może nie być konieczne, czy pliki w pamięci podręcznej są sprawdzane względem plików serwera końcowego. Z drugiej strony, jeśli chcesz upewnić się, że pliki są zawsze aktualne w magazynie zdalnym, wybierz model, który często sprawdza.

Te trzy opcje obejmują większość sytuacji:

* **Duże lub rzadkie odczyty** — przyspiesza dostęp do odczytu do plików statycznych lub rzadko zmienianych.

  Ta opcja powoduje buforowanie plików z odczytów klienta, ale natychmiast przekazuje zapis klienta do magazynu na zadomowie. Pliki przechowywane w pamięci podręcznej nie są automatycznie porównywane z plikami na woluminie magazynu NFS.

  Nie należy używać tej opcji, jeśli istnieje ryzyko, że plik może zostać zmodyfikowany bezpośrednio w systemie magazynu bez wcześniejszego zapisu w pamięci podręcznej. W takim przypadku buforowana wersja pliku nie będzie zsynchronizowana z plikiem back-end.

* **Więcej niż 15%** zapisu — ta opcja przyspiesza zarówno odczyt, jak i zapis.

  Odczyty klienta i zapis klienta są buforowane. Przyjmuje się, że pliki w pamięci podręcznej są nowsze niż pliki w systemie magazynu na zakładzie. Buforowane pliki są automatycznie sprawdzane tylko w przypadku plików w magazynie na zamecie co osiem godzin. Zmodyfikowane pliki w pamięci podręcznej są zapisywane w systemie magazynowania na zakładzie przez 20 minut bez żadnych dodatkowych zmian.

  Nie należy używać tej opcji, jeśli dowolny klient bezpośrednio zainstaluje wolumin magazynu na zadzie, ponieważ istnieje ryzyko, że będzie mieć nieaktualne pliki.

* Klienci zapis do obiektu docelowego **systemu plików NFS z** pominięciem pamięci podręcznej — wybierz tę opcję, jeśli dowolny klient w przepływie pracy zapisuje dane bezpośrednio w systemie magazynu bez wcześniejszego zapisu w pamięci podręcznej lub jeśli chcesz zoptymalizować spójność danych.

  Pliki, których żądają klienci, są buforowane, ale wszelkie zmiany tych plików z klienta są natychmiast przekazywane do systemu magazynowania na zakładzie. Pliki w pamięci podręcznej są często sprawdzane pod kątem wersji serwera końcowego w celu aktualizacji. Ta weryfikacja zapewnia spójność danych, gdy pliki są zmieniane bezpośrednio w systemie magazynu, a nie za pośrednictwem pamięci podręcznej.

Aby uzyskać szczegółowe informacje o innych opcjach, zobacz [Understand usage models (Informacje o modelach użycia).](cache-usage-models.md)

Ta tabela zawiera podsumowanie różnic między wszystkimi modelami użycia:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> Wartość **Weryfikacja zaległa** pokazuje, kiedy pamięć podręczna automatycznie porównuje swoje pliki z plikami źródłowymi w magazynie zdalnym. Można jednak wyzwolić porównanie, wysyłając żądanie klienta, które zawiera operację readdirplus w systemie magazynu na zakładzie. Readdirplus to standardowy interfejs API systemu plików NFS (nazywany również rozszerzonym odczytem), który zwraca metadane katalogu, co powoduje, że pamięć podręczna porównuje i aktualizuje pliki.

### <a name="create-an-nfs-storage-target"></a>Tworzenie obiektu docelowego magazynu NFS

### <a name="portal"></a>[Portal](#tab/azure-portal)

Z poziomu Azure Portal pamięci podręcznej otwórz wystąpienie pamięci podręcznej i kliknij pozycję **Miejsca docelowe** magazynu na lewym pasku bocznym.

![zrzut ekranu przedstawiający > docelowego magazynu, z dwoma istniejącymi obiektami docelowymi magazynu w tabeli i wyróżnieniem wokół przycisku + Dodaj miejsce docelowe magazynu nad tabelą](media/add-storage-target-button.png)

Strona **Miejsca docelowe** magazynu zawiera listę wszystkich istniejących obiektów docelowych i udostępnia link do dodawania nowego.

Kliknij przycisk **Dodaj miejsce docelowe** magazynu.

![Zrzut ekranu przedstawiający stronę dodawania miejsca docelowego magazynu ze zdefiniowanym obiektem docelowym systemu plików NFS](media/add-nfs-target.png)

Podaj te informacje dla obiektu docelowego magazynu z kopią zapasową systemu plików NFS:

* **Nazwa miejsca docelowego** magazynu — ustaw nazwę identyfikującą ten obiekt docelowy magazynu w Azure HPC Cache.

* **Typ docelowy** — wybierz **pozycję NFS.**

* **Nazwa hosta** — wprowadź adres IP lub w pełni kwalifikowaną nazwę domeny dla systemu magazynu NFS. (Użyj nazwy domeny tylko wtedy, gdy pamięć podręczna ma dostęp do serwera DNS, który może rozpoznać nazwę).

* **Model użycia** — wybierz jeden z profilów buforowania danych na podstawie przepływu pracy, zgodnie z opisem w powyższej części [Wybieranie modelu](#choose-a-usage-model) użycia.

Po zakończeniu kliknij przycisk **OK,** aby dodać miejsce docelowe magazynu.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

Użyj polecenia interfejsu wiersza polecenia platformy Azure [az hpc-cache nfs-storage-target add,](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add) aby utworzyć miejsce docelowe magazynu.

> [!NOTE]
> Polecenia interfejsu wiersza polecenia platformy Azure wymagają obecnie utworzenia ścieżki przestrzeni nazw podczas dodawania miejsca docelowego magazynu. Różni się to od procesu używanego z interfejsem Azure Portal interfejsu.

Oprócz nazwy pamięci podręcznej i grupy zasobów pamięci podręcznej należy podać następujące wartości:

* ``--name`` - Ustaw nazwę identyfikującą ten obiekt docelowy magazynu w Azure HPC Cache.
* ``--nfs3-target`` - Adres IP systemu magazynu NFS. (W tym miejscu możesz użyć w pełni kwalifikowanej nazwy domeny, jeśli pamięć podręczna ma dostęp do serwera DNS, który może ją rozpoznać).
* ``--nfs3-usage-model`` — Jeden z profilów buforowania danych opisany w powyższej części wybieranie [modelu](#choose-a-usage-model)użycia.

  Sprawdź nazwy modeli użycia za pomocą polecenia [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list).

* ``--junction`` - Parametr połączenia łączy wirtualną ścieżkę pliku klienta ze ścieżką eksportu w systemie magazynu.

  Obiekt docelowy magazynu NFS może mieć wiele ścieżek wirtualnych, o ile każda ścieżka reprezentuje inny eksport lub podkatalog w tym samym systemie magazynowania. Utwórz wszystkie ścieżki dla jednego systemu magazynowania na jednym docelowym magazynie.

  Ścieżki przestrzeni [nazw można dodawać i edytować](add-namespace-paths.md) w miejscu docelowym magazynu w dowolnym momencie.

  Parametr ``--junction`` używa tych wartości:

  * ``namespace-path`` — ścieżka pliku wirtualnego skierowanego do klienta
  * ``nfs-export`` — Eksport systemu magazynu do skojarzenia ze ścieżką klienta
  * ``target-path`` (opcjonalnie) — podkatalog eksportu, jeśli jest potrzebny

  Przykład: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Przeczytaj [temat Configure aggregated namespace (Konfigurowanie zagregowanej przestrzeni](hpc-cache-namespace.md) nazw), aby dowiedzieć się więcej o funkcji wirtualnej przestrzeni nazw.

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

## <a name="add-a-new-adls-nfs-storage-target-preview"></a>Dodawanie nowego obiektu docelowego magazynu ADLS-NFS (wersja zapoznawcza)

Obiekty docelowe magazynu ADLS-NFS używają kontenerów obiektów blob platformy Azure, które obsługują protokół NFS 3.0.

> [!NOTE]
> Obsługa protokołu NFS 3.0 dla usługi Azure Blob Storage jest w publicznej wersji zapoznawczej. Dostępność jest ograniczona, a funkcje mogą ulec zmianie od teraz do czasu, gdy funkcja stanie się ogólnie dostępna. Nie używaj technologii w wersji zapoznawczej w systemach produkcyjnych.
>
> Przeczytaj [informacje na temat obsługi protokołu NFS 3.0.](../storage/blobs/network-file-system-protocol-support.md)

Obiekty docelowe magazynu ADLS-NFS mają pewne podobieństwa do obiektów docelowych usługi Blob Storage, a niektóre z obiektami docelowymi magazynu NFS. Na przykład:

* Podobnie jak w przypadku obiektu docelowego usługi Blob Storage, musisz nadać Azure HPC Cache dostępu [do konta magazynu.](#add-the-access-control-roles-to-your-account)
* Podobnie jak w przypadku obiektu docelowego magazynu NFS, należy ustawić [model użycia pamięci podręcznej](#choose-a-usage-model).
* Ponieważ kontenery obiektów blob z obsługą systemu plików NFS mają strukturę hierarchiczną zgodną z systemem plików NFS, nie trzeba używać pamięci podręcznej do pozyskania danych, a kontenery są odczytywane przez inne systemy NFS. Możesz wstępnie załadować dane w kontenerze ADLS-NFS, następnie dodać je do magazynu HPC Cache jako miejsce docelowe magazynu, a następnie uzyskać dostęp do danych później spoza HPC Cache. Jeśli używasz standardowego kontenera obiektów blob jako docelowego HPC Cache magazynu, dane są zapisywane w zastrzeżonym formacie i są dostępne tylko z innych produktów Azure HPC Cache zgodnych z systemem .

Przed utworzeniem obiektu docelowego magazynu ADLS-NFS należy utworzyć konto magazynu z obsługą systemu plików NFS. Postępuj zgodnie ze wskazówkami [w te](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) Azure HPC Cache wymagania wstępne i instrukcjami w te tematu Mount Blob storage by using NFS (Zainstaluj usługę Blob Storage przy użyciu systemu [plików NFS).](../storage/blobs/network-file-system-protocol-support-how-to.md) Po skonfigurowaniu konta magazynu możesz utworzyć nowy kontener podczas tworzenia miejsca docelowego magazynu.

Przeczytaj [temat Use NFS-mounted blob storage with Azure HPC Cache](nfs-blob-considerations.md) (Korzystanie z magazynu obiektów blob zainstalowanego na Azure HPC Cache NFS), aby dowiedzieć się więcej o tej konfiguracji.

Aby utworzyć obiekt docelowy magazynu ADLS-NFS, otwórz stronę **Dodawanie miejsca** docelowego magazynu w Azure Portal. (Dodatkowe metody są w rozwoju).

![Zrzut ekranu przedstawiający stronę dodawania miejsca docelowego magazynu ze zdefiniowanym obiektem docelowym usługi ADLS-NFS](media/add-adls-target.png)

Wprowadź te informacje.

* **Nazwa miejsca docelowego** magazynu — ustaw nazwę identyfikującą ten obiekt docelowy magazynu w Azure HPC Cache.
* **Typ docelowy** — wybierz pozycję **ADLS-NFS.**
* **Konto magazynu** — wybierz konto, którego chcesz użyć. Jeśli konto magazynu z włączoną obsługą systemu plików NFS nie jest wyświetlane na liście, sprawdź, czy jest ono zgodne z warunkami wstępnymi i czy pamięć podręczna może uzyskać do niego dostęp.

  Należy autoryzować wystąpienie pamięci podręcznej w celu uzyskania dostępu do konta magazynu zgodnie z opisem w tece [Dodawanie ról dostępu.](#add-the-access-control-roles-to-your-account)

* **Kontener magazynu** — wybierz kontener obiektów blob z obsługą systemu plików NFS dla tego obiektu docelowego lub kliknij **pozycję Utwórz nowy.**

* **Model użycia** — wybierz jeden z profilów buforowania danych na podstawie przepływu pracy, zgodnie z opisem w [powyższej](#choose-a-usage-model) części Wybieranie modelu użycia.

Po zakończeniu kliknij przycisk **OK,** aby dodać miejsce docelowe magazynu.

## <a name="view-storage-targets"></a>Wyświetlanie obiektów docelowych magazynu

Możesz użyć interfejsu Azure Portal wiersza polecenia platformy Azure, aby wyświetlić obiekty docelowe magazynu już zdefiniowane dla pamięci podręcznej.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Z poziomu Azure Portal pamięci podręcznej otwórz wystąpienie pamięci podręcznej i kliknij pozycję Miejsca docelowe **magazynu,** która znajduje się pod nagłówkiem Ustawienia na lewym pasku bocznym. Strona obiektów docelowych magazynu zawiera listę wszystkich istniejących elementów docelowych i kontrolek dodawania lub usuwania.

Kliknij nazwę obiektu docelowego magazynu, aby otworzyć jego stronę szczegółów.

Przeczytaj [temat Edytowanie obiektów docelowych magazynu,](hpc-cache-edit-storage.md) aby dowiedzieć się więcej.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

Użyj opcji [az hpc-cache storage-target list,](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) aby wyświetlić istniejące obiekty docelowe magazynu dla pamięci podręcznej. Należy podać nazwę pamięci podręcznej i grupę zasobów (chyba że ustawiono ją globalnie).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Użyj [az hpc-cache storage-target show,](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) aby wyświetlić szczegółowe informacje o konkretnym docelowym magazynie. (Określ miejsce docelowe magazynu według nazwy).

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

Po utworzeniu obiektów docelowych magazynu kontynuuj wykonywanie tych zadań, aby przygotować pamięć podręczną do użycia:

* [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md)
* [Instalowanie pamięci podręcznej usługi Azure HPC Cache](hpc-cache-mount.md)
* [Przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md)

Jeśli musisz zaktualizować jakiekolwiek ustawienia, możesz [edytować miejsce docelowe magazynu.](hpc-cache-edit-storage.md)

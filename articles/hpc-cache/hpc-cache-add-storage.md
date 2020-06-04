---
title: Dodawanie magazynu do pamięci podręcznej platformy Azure HPC
description: Jak zdefiniować cele magazynu, aby pamięć podręczna platformy Azure HPC mogła używać lokalnego systemu plików NFS lub kontenerów obiektów blob platformy Azure do przechowywania długoterminowych plików
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: v-erkel
ms.openlocfilehash: 9d08526334a9891788df63b156cdf65f55c587ca
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344938"
---
# <a name="add-storage-targets"></a>Dodawanie lokalizacji docelowych magazynu

*Cele magazynu* są magazynem zaplecza dla plików, do których dostęp odbywa się za pomocą pamięci podręcznej platformy Azure HPC. Można dodać magazyn systemu plików NFS (taki jak lokalny system sprzętowy) lub zapisać dane w usłudze Azure Blob.

Można zdefiniować do dziesięciu różnych miejsc docelowych magazynu dla jednej pamięci podręcznej. Pamięć podręczna przedstawia wszystkie cele magazynu w jednej zagregowanej przestrzeni nazw.

Należy pamiętać, że eksporty magazynu muszą być dostępne z sieci wirtualnej pamięci podręcznej. W przypadku lokalnego magazynu sprzętu może być konieczne skonfigurowanie serwera DNS, który może rozpoznawać nazwy hostów dla dostępu do magazynu NFS. Więcej informacji można znaleźć w temacie [dostęp do usługi DNS](hpc-cache-prereqs.md#dns-access).

Dodaj elementy docelowe magazynu po utworzeniu pamięci podręcznej. Procedura jest nieco inna w zależności od tego, czy dodawana jest usługa Azure Blob Storage, czy eksport systemu plików NFS. Poniżej znajdują się szczegółowe informacje dotyczące każdego z nich.

Kliknij poniższy obraz, aby obejrzeć [film wideo](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) przedstawiający tworzenie pamięci podręcznej i Dodawanie miejsca docelowego magazynu.

[![Miniatura wideo: Azure HPC cache: Setup (kliknij, aby odwiedzić stronę wideo)](media/video4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="open-the-storage-targets-page"></a>Otwórz stronę miejsc docelowych magazynu

W Azure Portal Otwórz wystąpienie pamięci podręcznej, a następnie kliknij pozycję **cele magazynu** na lewym pasku bocznym. Na stronie miejsce docelowe magazynu są wyświetlane wszystkie istniejące elementy docelowe i nadające się do dodania łącze.

![zrzut ekranu przedstawiający link miejsca do magazynowania na pasku bocznym w obszarze Konfigurowanie nagłówka, który jest między ustawieniami nagłówków kategorii i monitorowaniem](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Dodaj nowy element docelowy usługi Azure Blob Storage

Nowy obiekt docelowy magazynu obiektów BLOB wymaga pustego kontenera obiektów blob lub kontenera, który został wypełniony danymi w formacie systemu plików w chmurze usługi Azure HPC. Przeczytaj więcej na temat wstępnego ładowania kontenera obiektów BLOB w temacie [przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md).

Nowy kontener można utworzyć na podstawie tej strony tuż przed dodaniem go.

![zrzut ekranu przedstawiający stronę Dodawanie miejsca docelowego magazynu, wypełniony informacjami o nowym obiekcie docelowym usługi Azure Blob Storage](media/hpc-cache-add-blob.png)

Aby zdefiniować kontener obiektów blob platformy Azure, wprowadź te informacje.

* **Nazwa docelowego magazynu** — Ustaw nazwę identyfikującą ten element docelowy magazynu w pamięci podręcznej platformy Azure HPC.
* **Typ docelowy** — wybierz **obiekt BLOB**.
* **Konto magazynu** — wybierz konto, którego chcesz użyć.

  Musisz autoryzować wystąpienie pamięci podręcznej, aby uzyskać dostęp do konta magazynu, zgodnie z opisem w temacie [Dodawanie ról dostępu](#add-the-access-control-roles-to-your-account).

  Aby uzyskać informacje o rodzaju konta magazynu, którego można użyć, przeczytaj temat [wymagania dotyczące usługi BLOB Storage](hpc-cache-prereqs.md#blob-storage-requirements).

* **Kontener magazynu** — wybierz kontener obiektów BLOB dla tego obiektu docelowego lub kliknij pozycję **Utwórz nowy**.

  ![zrzut ekranu przedstawiający okno dialogowe, w którym można określić poziom nazw i dostępu (prywatny) dla nowego kontenera](media/add-blob-new-container.png)

* **Ścieżka do wirtualnej przestrzeni nazw** — Ustaw ścieżkę pliku dla klienta dla tego miejsca docelowego magazynu. Przeczytaj temat [Konfigurowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md) , aby dowiedzieć się więcej o funkcji wirtualnej przestrzeni nazw.

Po zakończeniu kliknij przycisk **OK** , aby dodać miejsce docelowe magazynu.

> [!NOTE]
> Jeśli Zapora konta magazynu jest ustawiona w taki sposób, aby ograniczyć dostęp tylko do wybranych sieci, użyj tymczasowego obejścia udokumentowanego w obszarze [Pracuj wokół ustawień zapory konta magazynu obiektów BLOB](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Dodaj role kontroli dostępu do swojego konta

Pamięć podręczna Azure HPC używa [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) do autoryzowania usługi pamięci podręcznej w celu uzyskania dostępu do konta magazynu dla obiektów docelowych usługi Azure Blob Storage.

Właściciel konta magazynu musi jawnie dodać rolę współautor danych [współautor konta magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) i [obiektu blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) dla użytkownika "dostawca zasobów pamięci podręcznej usługi HPC".

Można to zrobić wcześniej lub przez kliknięcie linku na stronie, w którym można dodać obiekt docelowy magazynu obiektów BLOB. Należy pamiętać, że może upłynąć do 5 minut, zanim ustawienia roli są propagowane za pomocą środowiska platformy Azure, więc przed utworzeniem elementu docelowego magazynu należy poczekać kilka minut.

Procedura dodawania ról RBAC:

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

## <a name="add-a-new-nfs-storage-target"></a>Dodaj nowy element docelowy magazynu NFS

Obiekt docelowy magazynu NFS ma więcej pól niż obiekt docelowy magazynu obiektów BLOB. Te pola określają sposób osiągnięcia eksportu magazynu i wydajnej pamięci podręcznej danych. Ponadto obiekt docelowy magazynu NFS umożliwia utworzenie wielu ścieżek przestrzeni nazw, Jeśli host NFS ma więcej niż jeden dostępny eksport.

![Zrzut ekranu przedstawiający stronę Dodawanie miejsca docelowego magazynu z zdefiniowanym elementem docelowym NFS](media/add-nfs-target.png)

> [!NOTE]
> Przed utworzeniem miejsca docelowego magazynu NFS upewnij się, że system magazynu jest dostępny z pamięci podręcznej platformy Azure HPC i spełnia wymagania dotyczące uprawnień. Tworzenie docelowego magazynu zakończy się niepowodzeniem, jeśli pamięć podręczna nie będzie mogła uzyskać dostępu do systemu magazynu. Aby uzyskać szczegółowe informacje, zapoznaj się z [wymaganiami dotyczącymi magazynu NFS](hpc-cache-prereqs.md#nfs-storage-requirements) i [Rozwiązywanie problemów z usługą konfiguracja serwera nas i docelowymi](troubleshoot-nas.md)

Podaj te informacje dla miejsca docelowego magazynu z kopią zapasową NFS:

* **Nazwa docelowego magazynu** — Ustaw nazwę identyfikującą ten element docelowy magazynu w pamięci podręcznej platformy Azure HPC.

* **Typ docelowy** — wybierz system **plików NFS**.

* **Nazwa hosta** — wprowadź adres IP lub w pełni kwalifikowaną nazwę domeny dla systemu magazynu NFS. (Użyj nazwy domeny tylko wtedy, gdy pamięć podręczna ma dostęp do serwera DNS, który może rozpoznać nazwę).

* **Model użycia** — wybierz jeden z profili buforowania danych opartych na przepływie pracy, zgodnie z opisem w temacie [Wybierz model użycia](#choose-a-usage-model)poniżej.

### <a name="nfs-namespace-paths"></a>Ścieżki przestrzeni nazw NFS

Obiekt docelowy magazynu NFS może mieć wiele ścieżek wirtualnych, o ile każda ścieżka reprezentuje inny eksport lub podkatalog w tym samym systemie magazynu.

Utwórz wszystkie ścieżki z jednego miejsca docelowego magazynu.

[Ścieżki przestrzeni nazw można dodawać i edytować](hpc-cache-edit-storage.md) w miejscu docelowym magazynu w dowolnym momencie.

Wypełnij te wartości dla każdej ścieżki przestrzeni nazw:

* **Ścieżka do wirtualnej przestrzeni nazw** — Ustaw ścieżkę pliku dla klienta dla tego miejsca docelowego magazynu. Przeczytaj temat [Konfigurowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md) , aby dowiedzieć się więcej o funkcji wirtualnej przestrzeni nazw.

* **Ścieżka eksportowania NFS** — wprowadź ścieżkę do eksportu systemu plików NFS.

* **Ścieżka podkatalogu** — Jeśli chcesz zainstalować określony podkatalog eksportu, wprowadź go tutaj. Jeśli nie, pozostaw to pole puste.

Po zakończeniu kliknij przycisk **OK** , aby dodać miejsce docelowe magazynu.

### <a name="choose-a-usage-model"></a>Wybierz model użycia
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Podczas tworzenia miejsca docelowego magazynu, które wskazuje system magazynu NFS, należy wybrać model użycia dla tego celu. Ten model określa, w jaki sposób dane są buforowane.

Dostępne są trzy opcje:

* **Czytaj duże, rzadko** występujące zapisy — Użyj tej opcji, jeśli chcesz przyspieszyć dostęp do odczytu do plików, które są statyczne lub rzadko zmieniane.

  Ta opcja powoduje buforowanie plików, które są odczytywane przez klientów, ale natychmiast przekazuje do magazynu zaplecza. Pliki przechowywane w pamięci podręcznej nigdy nie są porównywane z plikami znajdującymi się na woluminie magazynu NFS.

  Nie należy używać tej opcji, jeśli istnieje ryzyko, że plik może być modyfikowany bezpośrednio w systemie magazynu bez wcześniejszego zapisania go w pamięci podręcznej. W takim przypadku buforowana wersja pliku nigdy nie będzie aktualizowana o zmiany z zaplecza, a zestaw danych może stać się niespójny.

* **Więcej niż 15% zapisów** — ta opcja przyspiesza zarówno wydajność odczytu, jak i zapisu. W przypadku korzystania z tej opcji Wszyscy klienci muszą uzyskać dostęp do plików za pośrednictwem pamięci podręcznej platformy Azure HPC zamiast bezpośrednio zainstalować magazyn zaplecza. Pliki w pamięci podręcznej będą miały ostatnio wprowadzone zmiany, które nie są przechowywane w zapleczu.

  W tym modelu użycia pliki w pamięci podręcznej nie są sprawdzane względem plików w magazynie zaplecza. Założono, że w pamięci podręcznej znajduje się nowsza wersja pliku. Zmodyfikowany plik w pamięci podręcznej jest zapisywana w systemie magazynu zaplecza, po którym znajduje się w pamięci podręcznej przez godzinę bez dodatkowych zmian.

* **Klienci zapisują w miejscu DOCELOWYM NFS, pomijając pamięć podręczną** — wybierz tę opcję, jeśli jakikolwiek klient w przepływie pracy zapisuje dane bezpośrednio w systemie magazynu bez wcześniejszego zapisu w pamięci podręcznej. Pliki, które są przechowywane w pamięci podręcznej żądań klientów, ale wszelkie zmiany tych plików z klienta są natychmiast przesyłane z powrotem do systemu magazynu zaplecza.

  W tym modelu użycia pliki w pamięci podręcznej są często sprawdzane względem wersji zaplecza dla aktualizacji. Ta weryfikacja pozwala na zmianę plików poza pamięcią podręczną przy zachowaniu spójności danych.

Ta tabela zawiera podsumowanie różnic między modelami użycia:

| Model użycia | Tryb buforowania | Weryfikacja zaplecza | Maksymalne opóźnienie zapisu |
| ---- | ---- | ---- | ---- |
| Czytaj duże, rzadko występujące zapisy | Odczyt | Nigdy | Brak |
| Ponad 15% zapisów | Odczyt/zapis | Nigdy | 1 godzina |
| Klienci pomijają pamięć podręczną | Odczyt | 30 sekund | Brak |

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obiektów docelowych magazynu należy wziąć pod uwagę jedno z następujących zadań:

* [Instalowanie pamięci podręcznej usługi Azure HPC Cache](hpc-cache-mount.md)
* [Przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md)

Jeśli trzeba zaktualizować dowolne ustawienia, można [edytować miejsce docelowe magazynu](hpc-cache-edit-storage.md).

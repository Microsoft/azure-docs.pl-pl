---
title: Dodawanie magazynu do pamięci podręcznej HPC platformy Azure
description: Jak zdefiniować obiekty docelowe magazynu, aby pamięć podręczna HPC platformy Azure mogła używać lokalnego systemu NFS lub kontenerów obiektów blob platformy Azure do długoterminowego przechowywania plików
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: cecafd9209b095270e9a06ca59ffef162326efc2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538005"
---
# <a name="add-storage-targets"></a>Dodawanie lokalizacji docelowych magazynu

*Obiekty docelowe magazynu* są magazynem zaplecza dla plików, które są dostępne za pośrednictwem pamięci podręcznej HPC platformy Azure. Można dodać magazyn systemu plików NFS (np. lokalny system sprzętowy) lub przechowywać dane w usłudze Azure Blob.

Można zdefiniować maksymalnie dziesięć różnych obiektów docelowych magazynu dla jednej pamięci podręcznej. Pamięć podręczna przedstawia wszystkie obiekty docelowe magazynu w jednym zagregowanym obszarze nazw.

Należy pamiętać, że eksportowanie magazynu musi być dostępne z sieci wirtualnej pamięci podręcznej. W przypadku lokalnego magazynu sprzętowego może być konieczne skonfigurowanie serwera DNS, który może rozpoznawać nazwy hostów dla dostępu do magazynu NFS. Więcej informacji można przeczytać w programie [Dostęp do systemu DNS](hpc-cache-prereqs.md#dns-access).

Dodaj obiekty docelowe magazynu po utworzeniu pamięci podręcznej. Procedura jest nieco inna w zależności od tego, czy dodajesz magazyn obiektów Blob platformy Azure, czy eksport nfs. Szczegóły dla każdego z nich znajdują się poniżej.

## <a name="open-the-storage-targets-page"></a>Otwórz stronę obiektów docelowych magazynu

W witrynie Azure Portal otwórz wystąpienie pamięci podręcznej i kliknij pozycję **Obiekty docelowe magazynu** na lewym pasku bocznym. Strona obiektów docelowych magazynu zawiera listę wszystkich istniejących obiektów docelowych i zawiera łącze, aby dodać nowy.

![zrzut ekranu łącza cele przechowywania na pasku bocznym, pod nagłówkiem Konfiguruj, który znajduje się między nagłówkami kategorii Ustawienia i Monitorowanie](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Dodawanie nowego obiektu docelowego magazynu obiektów Blob platformy Azure

Nowy obiekt docelowy magazynu obiektów Blob wymaga pustego kontenera obiektów Blob lub kontenera, który jest wypełniany danymi w formacie systemu plików w chmurze hpc cache platformy Azure. Dowiedz się więcej o wstępnym ładowaniu kontenera obiektów Blob w [obszarze Przenoszenie danych do magazynu obiektów Blob platformy Azure.](hpc-cache-ingest.md)

Możesz utworzyć nowy kontener z tej strony tuż przed dodaniem.

![zrzut ekranu przedstawiający stronę docelową dodawania magazynu wypełnionego informacjami o nowym docelowym magazynie obiektów Blob platformy Azure](media/hpc-cache-add-blob.png)

Aby zdefiniować kontener obiektów blob platformy Azure, wprowadź te informacje.

* **Nazwa obiektu docelowego magazynu** — ustaw nazwę identyfikującą ten obiekt docelowy magazynu w pamięci podręcznej HPC platformy Azure.
* **Typ docelowy** — wybierz **obiekt blob**.
* **Konto magazynu** — wybierz konto, którego chcesz użyć.

  Aby uzyskać dostęp do konta magazynu, należy autoryzować wystąpienie pamięci podręcznej w sposób opisany w obszarze [Dodaj role dostępu](#add-the-access-control-roles-to-your-account).

  Aby uzyskać informacje na temat rodzaju konta magazynu, którego można użyć, przeczytaj [wymagania dotyczące magazynu obiektów Blob](hpc-cache-prereqs.md#blob-storage-requirements).

* **Kontener magazynu** — wybierz kontener obiektów blob dla tego obiektu docelowego lub kliknij przycisk **Utwórz nowy**.

  ![zrzut ekranu okna dialogowego w celu określenia nazwy i poziomu dostępu (prywatnego) dla nowego kontenera](media/add-blob-new-container.png)

* **Ścieżka wirtualnego obszaru nazw** — umożliwia ustawienie ścieżki pliku skierowanej do klienta dla tego miejsca docelowego magazynu. Przeczytaj [pozycję Konfigurowanie zagregowanego obszaru nazw,](hpc-cache-namespace.md) aby dowiedzieć się więcej o funkcji wirtualnego obszaru nazw.

Po zakończeniu kliknij przycisk **OK,** aby dodać miejsce docelowe magazynu.

> [!NOTE]
> Jeśli zapora konta magazynu jest ustawiona na ograniczenie dostępu tylko do "wybranych sieci", użyj tymczasowego obejścia problemu udokumentowanego w [sekcji Obejście ustawień zapory konta magazynu obiektów Blob](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Dodawanie ról kontroli dostępu do konta

Usługa HpC Cache usługi Azure używa [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) do autoryzowania usługi pamięci podręcznej w celu uzyskania dostępu do konta magazynu dla obiektów docelowych magazynu obiektów Blob platformy Azure.

Właściciel konta magazynu musi jawnie dodać role [Współautor konta magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) i współautor danych obiektów [blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) dla użytkownika "DOSTAWCA ZASOBÓW PAMIĘCI PODRĘCZNEJ HPC".

Można to zrobić z wyprzedzeniem lub klikając łącze na stronie, na której można dodać miejsce docelowe magazynu obiektów Blob. Należy pamiętać, że może upłynąć do pięciu minut dla ustawień roli do propagacji za pośrednictwem środowiska platformy Azure, więc należy odczekać kilka minut po dodaniu ról przed utworzeniem miejsca docelowego magazynu.

Kroki, aby dodać role RBAC:

1. Otwórz stronę **Kontrola dostępu (IAM)** dla konta magazynu. (Łącze na stronie **Dodaj miejsce docelowe** automatycznie otwiera tę stronę dla wybranego konta).

1. Kliknij **+** u góry strony i wybierz pozycję **Dodaj przypisanie roli**.

1. Wybierz rolę "Współautor konta magazynu" z listy.

1. W polu **Przypisz dostęp do** pola pozostaw wybraną wartość domyślną ("Użytkownik, grupa lub podmiot usługi Azure AD").  

1. W polu **Wybierz** wyszukaj hasło "hpc".  Ten ciąg powinien być zgodny z jedną jednostką usługi o nazwie "DOSTAWCA ZASOBÓW PAMIĘCI PODRĘCZNEJ HPC". Kliknij tego podmiotu, aby go zaznaczyć.

   > [!NOTE]
   > Jeśli wyszukiwanie "hpc" nie działa, spróbuj użyć ciągu "storagecache". Użytkownicy, którzy uczestniczyli w wersji zapoznawczej (przed GA) może być konieczne użycie starszej nazwy dla jednostki usługi.

1. Kliknij przycisk **Zapisz** u dołu.

1. Powtórz ten proces, aby przypisać rolę "Współautor danych obiektów blob magazynu".  

![zrzut ekranu przedstawiający dodawanie przypisania roli](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Dodawanie nowego miejsca docelowego magazynu NFS

Miejsce docelowe magazynu NFS ma więcej pól niż miejsce docelowe magazynu obiektów Blob. Te pola określają, jak dotrzeć do eksportu magazynu i jak skutecznie buforować swoje dane. Ponadto obiekt docelowy magazynu NFS umożliwia tworzenie wielu ścieżek obszaru nazw, jeśli host systemu plików NFS ma więcej niż jeden eksport dostępny.

![Zrzut ekranu przedstawiający stronę docelową dodawania magazynu ze zdefiniowanym obiektem docelowym systemu plików NFS](media/add-nfs-target.png)

> [!NOTE]
> Przed utworzeniem obiektu docelowego magazynu systemu plików NFS upewnij się, że system magazynu jest dostępny z pamięci podręcznej HPC platformy Azure i spełnia wymagania dotyczące uprawnień. Tworzenie obiektu docelowego magazynu zakończy się niepowodzeniem, jeśli pamięć podręczna nie może uzyskać dostępu do systemu magazynu. Przeczytaj [wymagania dotyczące pamięci masowej NFS](hpc-cache-prereqs.md#nfs-storage-requirements) i [rozwiąż problemy z konfiguracją serwera NAS i docelowymi problemami z pamięcią masową NFS,](troubleshoot-nas.md) aby uzyskać szczegółowe informacje.

Podaj te informacje dla obiektu docelowego magazynu wspieranego przez system NFS:

* **Nazwa obiektu docelowego magazynu** — ustaw nazwę identyfikującą ten obiekt docelowy magazynu w pamięci podręcznej HPC platformy Azure.

* **Typ docelowy** — wybierz **NFS**.

* **Nazwa hosta** — wprowadź adres IP lub w pełni kwalifikowaną nazwę domeny dla swojego systemu pamięci masowej NFS. (Nazwa domeny należy używać tylko wtedy, gdy pamięć podręczna ma dostęp do serwera DNS, który może rozwiązać tę nazwę).

* **Model użycia** — wybierz jeden z profilów buforowania danych na podstawie przepływu pracy, opisany w [wybierz model użycia](#choose-a-usage-model)poniżej.

### <a name="nfs-namespace-paths"></a>Ścieżki obszaru nazw systemu plików NFS

Obiekt docelowy magazynu systemu plików NFS może mieć wiele ścieżek wirtualnych, o ile każda ścieżka reprezentuje inny eksport lub podkatalog w tym samym systemie magazynowania.

Utwórz wszystkie ścieżki z jednego miejsca docelowego magazynu.

Ścieżki obszaru nazw można [dodawać i edytować](hpc-cache-edit-storage.md) w dowolnym momencie.

Wypełnij te wartości dla każdej ścieżki obszaru nazw:

* **Ścieżka wirtualnego obszaru nazw** — umożliwia ustawienie ścieżki pliku skierowanej do klienta dla tego miejsca docelowego magazynu. Przeczytaj [pozycję Konfigurowanie zagregowanego obszaru nazw,](hpc-cache-namespace.md) aby dowiedzieć się więcej o funkcji wirtualnego obszaru nazw.

* **Ścieżka eksportu nfs** — wprowadź ścieżkę do eksportu plików NFS.

* **Ścieżka podkatalogu** — jeśli chcesz zainstalować określony podkatalog eksportu, wprowadź go tutaj. Jeśli nie, pozostaw to pole puste.

Po zakończeniu kliknij przycisk **OK,** aby dodać miejsce docelowe magazynu.

### <a name="choose-a-usage-model"></a>Wybieranie modelu użycia
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Podczas tworzenia miejsca docelowego magazynu, który wskazuje system magazynu NFS, należy wybrać model użycia dla tego obiektu docelowego. Ten model określa sposób buforowania danych.

Dostępne są trzy opcje:

* **Odczyt ciężkich, rzadkich zapisów** — użyj tej opcji, jeśli chcesz przyspieszyć dostęp do odczytu do plików, które są statyczne lub rzadko zmieniane.

  Ta opcja buforuje pliki, które klienci czytają, ale przekazuje zapisy do magazynu zaplecza natychmiast. Pliki przechowywane w pamięci podręcznej nigdy nie są porównywane z plikami na woluminie magazynu NFS.

  Nie należy używać tej opcji, jeśli istnieje ryzyko, że plik może być modyfikowany bezpośrednio w systemie magazynu bez uprzedniego zapisania go w pamięci podręcznej. Jeśli tak się stanie, buforowana wersja pliku nigdy nie zostanie zaktualizowana o zmiany z zaplecza, a zestaw danych może stać się niespójny.

* **Więcej niż 15% zapisów** — ta opcja przyspiesza wydajność odczytu i zapisu. Korzystając z tej opcji, wszyscy klienci muszą uzyskać dostęp do plików za pośrednictwem pamięci podręcznej HPC platformy Azure zamiast instalować bezpośrednio magazyn zaplecza. Buforowane pliki będą miały ostatnie zmiany, które nie są przechowywane na zapleczu.

  W tym modelu użycia pliki w pamięci podręcznej nie są sprawdzane względem plików w magazynie zaplecza. Zakłada się, że buforowana wersja pliku jest bardziej aktualna. Zmodyfikowany plik w pamięci podręcznej jest zapisywany w systemie magazynu zaplecza po tym, jak był w pamięci podręcznej przez godzinę bez żadnych dodatkowych zmian.

* **Klienci zapisują do obiektu docelowego systemu plików NFS, pomijając pamięć podręczną** — wybierz tę opcję, jeśli klienci w przepływie pracy zapisują dane bezpośrednio do systemu magazynu bez uprzedniego zapisu w pamięci podręcznej. Pliki, które żądają klienci są buforowane, ale wszelkie zmiany tych plików z klienta są przekazywane z powrotem do systemu magazynu zaplecza natychmiast.

  W tym modelu użycia pliki w pamięci podręcznej są często sprawdzane względem wersji zaplecza dla aktualizacji. Ta weryfikacja umożliwia zmianę plików poza pamięcią podręczną przy zachowaniu spójności danych.

W tej tabeli podsumowano różnice modelu użycia:

| Model użycia | Tryb buforowania | Weryfikacja zaplecza | Maksymalne opóźnienie odpisu |
| ---- | ---- | ---- | ---- |
| Czytaj ciężkie, rzadkie zapisy | Odczyt | Nigdy | Brak |
| Więcej niż 15% zapisów | Odczyt/zapis | Nigdy | 1 godzina |
| Klienci omijają pamięć podręczną | Odczyt | 30 sekund | Brak |

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obiektów docelowych magazynu należy wziąć pod uwagę jedno z następujących zadań:

* [Instalowanie pamięci podręcznej HPC platformy Azure](hpc-cache-mount.md)
* [Przenoszenie danych do magazynu obiektów blob platformy Azure](hpc-cache-ingest.md)

Jeśli chcesz zaktualizować dowolne ustawienia, możesz [edytować miejsce docelowe magazynu](hpc-cache-edit-storage.md).

---
title: Skonfiguruj zagregowaną przestrzeń nazw pamięci podręcznej platformy Azure HPC
description: Jak utworzyć ścieżki związane z klientem dla magazynu zaplecza za pomocą pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: f45d5710f6feb8af2347ca298e07e8a4870d3d4f
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470470"
---
# <a name="set-up-the-aggregated-namespace"></a>Konfigurowanie zagregowanej przestrzeni nazw

Po utworzeniu obiektów docelowych magazynu należy również utworzyć dla nich ścieżki przestrzeni nazw. Maszyny klienckie używają tych ścieżek wirtualnych do uzyskiwania dostępu do plików za pomocą pamięci podręcznej zamiast bezpośredniego łączenia się z magazynem zaplecza. Ten system pozwala administratorom pamięci podręcznej zmieniać systemy przechowywania zaplecza bez konieczności ponownego zapisywania instrukcji klienta.

Zapoznaj się z tematem [Planowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md) , aby dowiedzieć się więcej o tej funkcji.

Na stronie **przestrzeń nazw** w Azure Portal są wyświetlane ścieżki używane przez klientów do uzyskiwania dostępu do danych za pomocą pamięci podręcznej. Ta strona służy do tworzenia, usuwania i zmieniania ścieżek przestrzeni nazw. Ścieżki przestrzeni nazw można również skonfigurować za pomocą interfejsu wiersza polecenia platformy Azure.

Wszystkie ścieżki dotyczące klientów, które zostały zdefiniowane dla tej pamięci podręcznej, są wymienione na stronie **przestrzeni nazw** . Cele magazynu, które nie mają zdefiniowanych ścieżek przestrzeni nazw, nie są jeszcze wyświetlane w tabeli.

Można sortować kolumny tabeli, aby lepiej zrozumieć zagregowaną przestrzeń nazw pamięci podręcznej. Kliknij strzałki w nagłówkach kolumn, aby posortować ścieżki.

[![zrzut ekranu przedstawiający stronę przestrzeni nazw portalu z dwiema ścieżkami w tabeli. Nagłówki kolumn: ścieżka przestrzeni nazw, miejsce docelowe magazynu, ścieżka eksportu i podkatalog eksportu oraz zasady dostępu klienta. Nazwy ścieżek w pierwszej kolumnie są łączami kliknięcia. Górne przyciski: Dodaj ścieżkę przestrzeni nazw, Odśwież, Usuń ](media/namespace-page.png)](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Dodawanie lub edytowanie ścieżek przestrzeni nazw

Aby klienci mogli uzyskać dostęp do miejsca docelowego magazynu, należy utworzyć co najmniej jedną ścieżkę przestrzeni nazw. (Aby uzyskać więcej informacji o dostępie klientów, zobacz temat [Instalowanie pamięci podręcznej platformy Azure HPC](hpc-cache-mount.md) ).

### <a name="blob-namespace-paths"></a>Ścieżki przestrzeni nazw obiektów BLOB

Obiekt docelowy magazynu obiektów blob platformy Azure może mieć tylko jedną ścieżkę przestrzeni nazw.

Postępuj zgodnie z poniższymi instrukcjami, aby ustawić lub zmienić ścieżkę do Azure Portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Na Azure Portal Załaduj stronę ustawienia **przestrzeni nazw** . Na tej stronie można dodawać, zmieniać i usuwać ścieżki przestrzeni nazw.

* **Dodaj nową ścieżkę:** Kliknij przycisk **+ Dodaj** znajdujący się u góry i Wypełnij informacje w panelu Edycja.

  ![Zrzut ekranu przedstawiający pola edycji Dodaj przestrzeń nazw z wybranym obiektem docelowym magazynu obiektów BLOB. Ścieżki eksportu i podkatalogu są ustawione na/i nie można ich edytować.](media/namespace-add-blob.png)

  * Wprowadź ścieżkę, która będzie używana przez klientów w celu uzyskania dostępu do tego miejsca docelowego magazynu.

  * Wybierz zasady dostępu, które mają być używane dla tej ścieżki. Dowiedz się więcej o dostosowywaniu dostępu klientów w temacie [Korzystanie z zasad dostępu klienta](access-policies.md).

  * Wybierz miejsce docelowe magazynu z listy rozwijanej. Jeśli obiekt docelowy magazynu obiektów BLOB ma już ścieżkę przestrzeni nazw, nie można go wybrać.

  * W przypadku celu usługi Azure Blob Storage ścieżki eksportu i podkatalogu są automatycznie ustawiane na ``/`` .

* **Zmień istniejącą ścieżkę:** Kliknij ścieżkę przestrzeni nazw. Zostanie otwarty panel edycji. Można zmodyfikować ścieżkę i zasady dostępu, ale nie można zmienić innego miejsca docelowego magazynu.

* **Usuń ścieżkę przestrzeni nazw:** Zaznacz pole wyboru po lewej stronie ścieżki, a następnie kliknij przycisk **Usuń** .

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Konfigurowanie interfejsu wiersza polecenia platformy Azure dla pamięci podręcznej platformy Azure HPC](./az-cli-prerequisites.md).

W przypadku korzystania z interfejsu wiersza polecenia platformy Azure należy dodać ścieżkę przestrzeni nazw podczas tworzenia miejsca docelowego magazynu. Aby uzyskać szczegółowe informacje, przeczytaj artykuł [Dodawanie nowego miejsca docelowego usługi Azure Blob Storage](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) .

Aby zaktualizować ścieżkę przestrzeni nazw obiektu docelowego, użyj polecenia [AZ HPC-cache BLOB-Storage-Target](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) . Argumenty polecenia Update są podobne do argumentów w poleceniu Create, z tą różnicą, że nie są przekazywane nazwy kontenera lub konta magazynu.

Nie można usunąć ścieżki przestrzeni nazw z docelowego obiektu BLOB Storage za pomocą interfejsu wiersza polecenia platformy Azure, ale można zastąpić ścieżkę inną wartością.

---

### <a name="nfs-namespace-paths"></a>Ścieżki przestrzeni nazw NFS

Obiekt docelowy magazynu NFS może mieć wiele ścieżek wirtualnych, o ile każda ścieżka reprezentuje inny eksport lub podkatalog w tym samym systemie magazynu.

Planując przestrzeń nazw dla miejsca docelowego magazynu NFS, należy pamiętać, że każda ścieżka musi być unikatowa i nie może być podkatalogiem innej ścieżki przestrzeni nazw. Na przykład jeśli masz ścieżkę przestrzeni nazw, która jest wywoływana ``/parent-a`` , nie można również tworzyć ścieżek przestrzeni nazw, takich jak ``/parent-a/user1`` i ``/parent-a/user2`` . Te ścieżki katalogów są już dostępne w przestrzeni nazw jako podkatalogi ``/parent-a`` .

Wszystkie ścieżki przestrzeni nazw dla systemu magazynu NFS są tworzone w jednym miejscu docelowym magazynu. Większość konfiguracji pamięci podręcznej może obsługiwać do dziesięciu ścieżek przestrzeni nazw na miejsce docelowe magazynu, ale większa konfiguracja może obsłużyć do 20.

Ta lista zawiera maksymalną liczbę ścieżek przestrzeni nazw na konfigurację.

* Do 2 GB/s przepływności:

  * 3 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 6 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 12 TB pamięci podręcznej-20 ścieżek przestrzeni nazw

* Do 4 GB/s przepływności:

  * 6 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 12 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 24 TB pamięci podręcznej-20 ścieżek przestrzeni nazw

* Do 8 GB/s przepływności:

  * 12 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 24 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 48 TB pamięci podręcznej-20 ścieżek przestrzeni nazw

Dla każdej ścieżki przestrzeni nazw systemu plików NFS podaj ścieżkę dostępną dla klienta, eksport systemu magazynu i opcjonalnie podkatalog eksportu.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Na Azure Portal Załaduj stronę ustawienia **przestrzeni nazw** . Na tej stronie można dodawać, edytować lub usuwać ścieżki przestrzeni nazw.

* **Aby dodać nową ścieżkę:** Kliknij przycisk **+ Dodaj** znajdujący się u góry i Wypełnij informacje w panelu Edycja.
* **Aby zmienić istniejącą ścieżkę:** Kliknij ścieżkę przestrzeni nazw. Zostanie otwarty panel edycji i można zmodyfikować ścieżkę.
* **Aby usunąć ścieżkę przestrzeni nazw:** Zaznacz pole wyboru po lewej stronie ścieżki, a następnie kliknij przycisk **Usuń** .

Wypełnij te wartości dla każdej ścieżki przestrzeni nazw:

* **Ścieżka przestrzeni nazw** — ścieżka pliku po stronie klienta.

* **Zasady dostępu klienta** — wybierz zasady dostępu, które mają być używane dla tej ścieżki. Dowiedz się więcej o dostosowywaniu dostępu klientów w temacie [Korzystanie z zasad dostępu klienta](access-policies.md).

* **Docelowy magazyn** — w przypadku tworzenia nowej ścieżki przestrzeni nazw wybierz miejsce docelowe magazynu z menu rozwijanego.

* **Ścieżka eksportu** — wprowadź ścieżkę do eksportu systemu plików NFS. Upewnij się, że nazwa eksportu została prawidłowo wpisana — Portal sprawdza poprawność składni dla tego pola, ale nie sprawdza eksportu do momentu przesłania zmiany.

* **Wyeksportuj podkatalog** — Jeśli chcesz, aby ta ścieżka instalowała określony podkatalog eksportu, wprowadź ją w tym miejscu. Jeśli nie, pozostaw to pole puste.

![zrzut ekranu strony przestrzeni nazw portalu z otwartą stroną Edytuj po prawej stronie. Formularz edycji pokazuje ustawienia ścieżki przestrzeni nazw docelowej magazynu NFS](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Konfigurowanie interfejsu wiersza polecenia platformy Azure dla pamięci podręcznej platformy Azure HPC](./az-cli-prerequisites.md).

W przypadku korzystania z interfejsu wiersza polecenia platformy Azure podczas tworzenia miejsca docelowego magazynu należy dodać co najmniej jedną ścieżkę przestrzeni nazw. Aby uzyskać szczegółowe informacje, przeczytaj temat [Dodawanie nowego miejsca docelowego magazynu NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) .

Aby zaktualizować ścieżkę przestrzeni nazw obiektu docelowego lub dodać dodatkowe ścieżki, użyj polecenia [AZ HPC-cache NFS-Storage-Target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) . Użyj ``--junction`` opcji, aby określić wszystkie ścieżki przestrzeni nazw, które chcesz.

Opcje używane dla polecenia Update są podobne do polecenia "Create", z tą różnicą, że nie są przekazywane informacje o systemie magazynu (adres IP lub nazwa hosta), a model użycia jest opcjonalny. Aby uzyskać więcej informacji na temat składni opcji, przeczytaj artykuł [Dodawanie nowego miejsca docelowego magazynu NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) ``--junction`` .

---

### <a name="adls-nfs-namespace-paths-preview"></a>Ścieżki przestrzeni nazw ADLS-NFS (wersja zapoznawcza)

Podobnie jak w przypadku zwykłego miejsca docelowego magazynu obiektów blob, magazyn ADLS systemu plików NFS ma tylko jeden eksport, więc może mieć tylko jedną ścieżkę przestrzeni nazw.

Postępuj zgodnie z poniższymi instrukcjami, aby ustawić lub zmienić ścieżkę Azure Portal.

Załaduj stronę ustawienia **przestrzeni nazw** .

* **Dodaj nową ścieżkę:** Kliknij przycisk **+ Dodaj** znajdujący się u góry i Wypełnij informacje w panelu Edycja.

  ![Zrzut ekranu przedstawiający pola edycji Dodawanie przestrzeni nazw z wybranym miejscem docelowym magazynu ADLS-NFS. Ścieżki eksportu i podkatalogu są ustawione na/i nie można ich edytować.](media/namespace-add-adls.png)

  * Wprowadź ścieżkę, która będzie używana przez klientów w celu uzyskania dostępu do tego miejsca docelowego magazynu.

  * Wybierz zasady dostępu, które mają być używane dla tej ścieżki. Dowiedz się więcej o dostosowywaniu dostępu klientów w temacie [Korzystanie z zasad dostępu klienta](access-policies.md).

  * Wybierz miejsce docelowe magazynu z listy rozwijanej. Jeśli miejsce docelowe magazynu ADLS-NFS ma już ścieżkę przestrzeni nazw, nie można go wybrać.

  * Dla obiektu docelowego magazynu ADLS-NFS ścieżki eksportu i podkatalogu są automatycznie ustawiane na ``/`` .

* **Zmień istniejącą ścieżkę:** Kliknij ścieżkę przestrzeni nazw. Zostanie otwarty panel edycji. Można zmodyfikować ścieżkę i zasady dostępu, ale nie można zmienić innego miejsca docelowego magazynu.

* **Usuń ścieżkę przestrzeni nazw:** Zaznacz pole wyboru po lewej stronie ścieżki, a następnie kliknij przycisk **Usuń** .

## <a name="next-steps"></a>Następne kroki

Po utworzeniu zagregowanej przestrzeni nazw dla obiektów docelowych magazynu można instalować klientów w pamięci podręcznej. Przeczytaj te artykuły, aby dowiedzieć się więcej.

* [Instalowanie pamięci podręcznej usługi Azure HPC Cache](hpc-cache-mount.md)
* [Przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md)

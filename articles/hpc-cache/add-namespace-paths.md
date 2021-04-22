---
title: Konfigurowanie zagregowanej Azure HPC Cache nazw
description: Jak tworzyć ścieżki dla klientów dla magazynu za pomocą usługi Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2cb8db4e73a8f4fa299031070bffc15a2b754d7e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870377"
---
# <a name="set-up-the-aggregated-namespace"></a>Konfigurowanie zagregowanej przestrzeni nazw

Po utworzeniu obiektów docelowych magazynu należy również utworzyć dla nich ścieżki przestrzeni nazw. Maszyny klienckie używają tych ścieżek wirtualnych do uzyskiwania dostępu do plików za pośrednictwem pamięci podręcznej zamiast bezpośredniego nawiązywania połączenia z magazynem za pomocą serwera. Ten system umożliwia administratorom pamięci podręcznej zmianę systemów magazynowania na zakładzie bez konieczności ponownego pisali instrukcje klienta.

Przeczytaj [temat Planowanie zagregowanej przestrzeni nazw,](hpc-cache-namespace.md) aby dowiedzieć się więcej o tej funkcji.

Na **stronie Przestrzeń** nazw w Azure Portal ścieżki, których klienci używają do uzyskiwania dostępu do danych za pośrednictwem pamięci podręcznej. Ta strona umożliwia tworzenie, usuwanie lub zmienianie ścieżek przestrzeni nazw. Ścieżki przestrzeni nazw można również skonfigurować przy użyciu interfejsu wiersza polecenia platformy Azure.

Wszystkie ścieżki dostępne dla klienta, które zostały zdefiniowane dla tej pamięci podręcznej, są wyświetlane na **stronie Przestrzeń** nazw. Obiekty docelowe magazynu, które nie mają jeszcze zdefiniowanych ścieżek przestrzeni nazw, nie są wyświetlane w tabeli.

Kolumny tabeli można sortować, aby lepiej zrozumieć zagregowaną przestrzeń nazw pamięci podręcznej. Kliknij strzałki w nagłówkach kolumn, aby posortować ścieżki.

[![zrzut ekranu przedstawiający stronę przestrzeni nazw portalu z dwiema ścieżkami w tabeli. Nagłówki kolumn: ścieżka przestrzeni nazw, miejsce docelowe magazynu, ścieżka eksportu i podkatalog Eksportuj oraz zasady dostępu klienta. Nazwy ścieżek w pierwszej kolumnie są linkami, które można klikać. Najważniejsze przyciski: Dodawanie ścieżki przestrzeni nazw, odświeżanie, usuwanie ](media/namespace-page.png)](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Dodawanie lub edytowanie ścieżek przestrzeni nazw

Zanim klienci będą mieć dostęp do miejsca docelowego magazynu, należy utworzyć co najmniej jedną ścieżkę przestrzeni nazw. (Przeczytaj [temat Mount the Azure HPC Cache](hpc-cache-mount.md) for more about client access (Zainstaluj Azure HPC Cache więcej informacji o dostępie klienta).

Jeśli niedawno dodano miejsce docelowe magazynu lub dostosowano zasady dostępu, utworzenie ścieżki przestrzeni nazw może potrwać minutę lub dwie.

### <a name="blob-namespace-paths"></a>Ścieżki przestrzeni nazw obiektów blob

Obiekt docelowy usługi Azure Blob Storage może mieć tylko jedną ścieżkę przestrzeni nazw.

Postępuj zgodnie z poniższymi instrukcjami, aby ustawić lub zmienić ścieżkę za pomocą interfejsu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Z poziomu Azure Portal załaduj stronę **Ustawienia** przestrzeni nazw. Na tej stronie możesz dodawać, zmieniać lub usuwać ścieżki przestrzeni nazw.

* **Dodaj nową ścieżkę:** Kliknij przycisk **+ Dodaj** u góry i wypełnij informacje w panelu edycji.

  ![Zrzut ekranu przedstawiający dodawanie pól edycji przestrzeni nazw z wybranym obiektem docelowym magazynu obiektów blob. Ścieżki eksportu i podkatalogu są ustawione na / i nie można ich edytować.](media/namespace-add-blob.png)

  * Wprowadź ścieżkę używaną przez klientów do uzyskiwania dostępu do tego miejsca docelowego magazynu.

  * Wybierz zasady dostępu do użycia dla tej ścieżki. Aby dowiedzieć się więcej na temat dostosowywania dostępu klienta, [zobacz Korzystanie z zasad dostępu klienta.](access-policies.md)

  * Wybierz miejsce docelowe magazynu z listy rozwijanej. Jeśli miejsce docelowe magazynu obiektów blob ma już ścieżkę przestrzeni nazw, nie można jej wybrać.

  * W przypadku obiektu docelowego usługi Azure Blob Storage ścieżki eksportu i podkatalogu są automatycznie ustawiane na ``/`` wartość .

* **Zmień istniejącą ścieżkę:** Kliknij ścieżkę przestrzeni nazw. Zostanie otwarty panel edycji. Można zmodyfikować ścieżkę i zasady dostępu, ale nie można zmienić na inny docelowy magazyn.

* **Usuń ścieżkę przestrzeni nazw:** Zaznacz pole wyboru po lewej stronie ścieżki i kliknij **przycisk** Usuń.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

W przypadku korzystania z interfejsu wiersza polecenia platformy Azure należy dodać ścieżkę przestrzeni nazw podczas tworzenia obiektu docelowego magazynu. Przeczytaj [temat Dodawanie nowego obiektu docelowego usługi Azure Blob Storage,](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) aby uzyskać szczegółowe informacje.

Aby zaktualizować ścieżkę przestrzeni nazw obiektu docelowego, użyj [polecenia az hpc-cache blob-storage-target update.](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) Argumenty polecenia update są podobne do argumentów w poleceniu create, z tą różnicą, że nazwa kontenera lub konto magazynu nie są przekazywać.

Nie można usunąć ścieżki przestrzeni nazw z obiektu docelowego magazynu obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure, ale ścieżkę można zastąpić inną wartością.

---

### <a name="nfs-namespace-paths"></a>Ścieżki przestrzeni nazw NFS

Obiekt docelowy magazynu NFS może mieć wiele ścieżek wirtualnych, o ile każda ścieżka reprezentuje inny eksport lub podkatalog w tym samym systemie magazynowania.

Podczas planowania przestrzeni nazw dla obiektu docelowego magazynu NFS należy pamiętać, że każda ścieżka musi być unikatowa i nie może być podkatalogami innej ścieżki przestrzeni nazw. Jeśli na przykład masz ścieżkę przestrzeni nazw o nazwie , nie możesz również tworzyć ścieżek ``/parent-a`` przestrzeni nazw, takich ``/parent-a/user1`` jak i ``/parent-a/user2`` . Te ścieżki katalogów są już dostępne w przestrzeni nazw jako podkatalogi ``/parent-a`` .

Wszystkie ścieżki przestrzeni nazw dla systemu magazynu NFS są tworzone na jednym docelowym magazynie. Większość konfiguracji pamięci podręcznej może obsługiwać maksymalnie dziesięć ścieżek przestrzeni nazw na obiekt docelowy magazynu, ale większe konfiguracje mogą obsługiwać do 20.

Ta lista zawiera maksymalną liczbę ścieżek przestrzeni nazw na konfigurację.

* Przepływność do 2 GB/s:

  * 3 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 6 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 12 TB pamięci podręcznej — 20 ścieżek przestrzeni nazw

* Przepływność do 4 GB/s:

  * 6 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 12 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 24 TB pamięci podręcznej — 20 ścieżek przestrzeni nazw

* Przepływność do 8 GB/s:

  * 12 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 24 TB pamięci podręcznej — 10 ścieżek przestrzeni nazw
  * 48 TB pamięci podręcznej — 20 ścieżek przestrzeni nazw

Dla każdej ścieżki przestrzeni nazw systemu plików NFS podaj ścieżkę klienta, eksport systemu magazynowania i opcjonalnie podkatalog eksportu.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Z poziomu Azure Portal załaduj stronę **Ustawienia** przestrzeni nazw. Na tej stronie możesz dodawać, edytować lub usuwać ścieżki przestrzeni nazw.

* **Aby dodać nową ścieżkę:** Kliknij przycisk **+ Dodaj** u góry i wypełnij informacje w panelu edycji.
* **Aby zmienić istniejącą ścieżkę:** Kliknij ścieżkę przestrzeni nazw. Zostanie otwarty panel edytowania, w przypadku których można zmodyfikować ścieżkę.
* **Aby usunąć ścieżkę przestrzeni nazw:** Zaznacz pole wyboru po lewej stronie ścieżki i kliknij **przycisk** Usuń.

Wypełnij następujące wartości dla każdej ścieżki przestrzeni nazw:

* **Ścieżka przestrzeni** nazw — ścieżka pliku klienta.

* **Zasady dostępu klienta —** wybierz zasady dostępu do użycia dla tej ścieżki. Aby dowiedzieć się więcej na temat dostosowywania dostępu klienta, [zobacz Korzystanie z zasad dostępu klienta.](access-policies.md)

* **Miejsce docelowe** magazynu — w przypadku tworzenia nowej ścieżki przestrzeni nazw wybierz miejsce docelowe magazynu z menu rozwijanego.

* **Ścieżka eksportu** — wprowadź ścieżkę do eksportu systemu plików NFS. Pamiętaj o prawidłowym wpisaniu nazwy eksportu — portal weryfikuje składnię tego pola, ale nie sprawdza eksportu, dopóki nie zostanie przesłana zmiana.

* **Podkatalog eksportu** — jeśli chcesz, aby ta ścieżka zainstaluje określony podkatalog eksportu, wprowadź go tutaj. Jeśli nie, pozostaw to pole puste.

![zrzut ekranu przedstawiający stronę przestrzeni nazw portalu z otwartą stroną edycji po prawej stronie. Formularz edycji zawiera ustawienia ścieżki docelowej przestrzeni nazw magazynu nfs](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

W przypadku korzystania z interfejsu wiersza polecenia platformy Azure podczas tworzenia miejsca docelowego magazynu należy dodać co najmniej jedną ścieżkę przestrzeni nazw. Aby uzyskać szczegółowe informacje, zobacz Dodawanie nowego obiektu [docelowego magazynu NFS.](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target)

Aby zaktualizować ścieżkę przestrzeni nazw obiektu docelowego lub dodać dodatkowe ścieżki, użyj [polecenia az hpc-cache nfs-storage-target update.](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update) Użyj opcji ``--junction`` , aby określić wszystkie ścieżki przestrzeni nazw.

Opcje używane dla polecenia aktualizacji są podobne do polecenia "create", z tą różnicą, że nie są używane informacje o systemie magazynu (adres IP lub nazwa hosta), a model użycia jest opcjonalny. Przeczytaj temat Add a new NFS storage target (Dodawanie nowego obiektu docelowego magazynu [NFS),](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) aby uzyskać więcej informacji o składni ``--junction`` opcji .

---

### <a name="adls-nfs-namespace-paths-preview"></a>Ścieżki przestrzeni nazw ADLS-NFS (wersja zapoznawcza)

Podobnie jak w przypadku zwykłego obiektu docelowego magazynu obiektów blob, obiekt docelowy magazynu ADLS-NFS ma tylko jeden eksport, więc może mieć tylko jedną ścieżkę przestrzeni nazw.

Postępuj zgodnie z poniższymi instrukcjami, aby ustawić lub zmienić ścieżkę za pomocą Azure Portal.

Załaduj **stronę Ustawienia** przestrzeni nazw.

* **Dodaj nową ścieżkę:** Kliknij przycisk **+ Dodaj** u góry i wypełnij informacje w panelu edycji.

  ![Zrzut ekranu przedstawiający dodawanie pól edycji przestrzeni nazw z wybranym obiektem docelowym magazynu ADLS-NFS. Ścieżki eksportu i podkatalogu są ustawione na / i nie można ich edytować.](media/namespace-add-adls.png)

  * Wprowadź ścieżkę używaną przez klientów do uzyskiwania dostępu do tego miejsca docelowego magazynu.

  * Wybierz zasady dostępu do użycia dla tej ścieżki. Aby dowiedzieć się więcej na temat dostosowywania dostępu klienta, [zobacz Korzystanie z zasad dostępu klienta.](access-policies.md)

  * Wybierz miejsce docelowe magazynu z listy rozwijanej. Jeśli miejsce docelowe magazynu ADLS-NFS ma już ścieżkę przestrzeni nazw, nie można go wybrać.

  * W przypadku obiektu docelowego magazynu ADLS-NFS ścieżki eksportu i podkatalogu są automatycznie ustawiane na ``/`` wartość .

* **Zmień istniejącą ścieżkę:** Kliknij ścieżkę przestrzeni nazw. Zostanie otwarty panel edycji. Można zmodyfikować ścieżkę i zasady dostępu, ale nie można zmienić na inny docelowy magazyn.

* **Usuń ścieżkę przestrzeni nazw:** Zaznacz pole wyboru po lewej stronie ścieżki i kliknij **przycisk** Usuń.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu zagregowanej przestrzeni nazw dla obiektów docelowych magazynu można zainstalować klientów w pamięci podręcznej. Przeczytaj te artykuły, aby dowiedzieć się więcej.

* [Instalowanie pamięci podręcznej usługi Azure HPC Cache](hpc-cache-mount.md)
* [Przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md)

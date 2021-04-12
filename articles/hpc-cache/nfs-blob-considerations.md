---
title: Używanie magazynu obiektów BLOB systemu plików NFS z pamięcią podręczną platformy Azure HPC
description: Opisuje procedury i ograniczenia dotyczące korzystania z magazynu obiektów BLOB ADLS-NFS z użyciem pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: d861a41d8cdeac548729ff341b3ffe27c0aa8152
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259981"
---
# <a name="use-nfs-mounted-blob-storage-preview-with-azure-hpc-cache"></a>Używanie magazynu obiektów BLOB zainstalowanego w systemie plików NFS (wersja zapoznawcza) z pamięcią podręczną Azure HPC

Począwszy od marca 2021, można użyć kontenerów obiektów BLOB zainstalowanych w systemie plików NFS z pamięcią podręczną platformy Azure HPC. Przeczytaj więcej na temat [obsługi protokołu NFS 3,0 w usłudze Azure Blob Storage](../storage/blobs/network-file-system-protocol-support.md) w witrynie dokumentacji usługi BLOB Storage.

> [!NOTE]
> Obsługa protokołu NFS 3,0 w usłudze Azure Blob Storage jest w wersji zapoznawczej i nie powinna być używana w środowiskach produkcyjnych. Sprawdź dostępność aktualizacji i więcej szczegółów w [dokumentacji obsługi protokołu NFS](../storage/blobs/network-file-system-protocol-support.md).

Pamięć podręczna Azure HPC używa magazynu obiektów BLOB obsługującego system plików NFS w docelowym typie magazynu ADLS-NFS. Te cele magazynu są podobne do zwykłych obiektów docelowych magazynu NFS, ale również nakładają się na zwykłe cele obiektów blob platformy Azure.

W tym artykule opisano strategie i ograniczenia, które należy zrozumieć w przypadku korzystania z obiektów docelowych magazynu ADLS-NFS.

Należy również zapoznać się z dokumentacją obiektów BLOB systemu plików NFS, w szczególności z tymi sekcjami, które opisują zgodne i niezgodne scenariusze:

* [Omówienie funkcji](../storage/blobs/network-file-system-protocol-support.md#applications-and-workloads-suited-for-this-feature)
* [Funkcje nie są jeszcze obsługiwane](../storage/blobs/network-file-system-protocol-support.md#azure-storage-features-not-yet-supported)
* [Zagadnienia dotyczące wydajności](../storage/blobs/network-file-system-protocol-support-performance.md)

## <a name="understand-consistency-requirements"></a>Informacje o wymaganiach dotyczących spójności

Pamięć podręczna HPC wymaga silnej spójności dla obiektów docelowych magazynu ADLS-NFS. Domyślnie magazyn obiektów blob z obsługą systemu plików NFS nie aktualizuje ściśle metadanych plików, co uniemożliwia buforowanie HPC z dokładnego porównania wersji plików.

Aby obejść tę różnicę, pamięć podręczna Azure HPC automatycznie wyłącza buforowanie atrybutu NFS w dowolnym kontenerze obiektów BLOB obsługującym system plików NFS używanym jako docelowy magazyn.

To ustawienie będzie nadal obowiązywać w okresie istnienia kontenera, nawet jeśli zostanie usunięte z pamięci podręcznej.

## <a name="preload-data-with-nfs-protocol"></a>Wstępne ładowanie danych przy użyciu protokołu NFS

W przypadku kontenera obiektów blob z obsługą systemu plików NFS *plik może być edytowany tylko przez ten sam protokół, który został użyty podczas tworzenia*. Oznacza to, że w przypadku użycia interfejsu API REST platformy Azure do wypełnienia kontenera nie można zaktualizować tych plików przy użyciu systemu plików NFS. Ponieważ pamięć podręczna platformy Azure HPC korzysta tylko z systemu plików NFS, nie może edytować plików, które zostały utworzone za pomocą interfejsu API REST platformy Azure.

Nie jest to problem z pamięcią podręczną, jeśli kontener jest pusty lub pliki zostały utworzone przy użyciu systemu plików NFS.

Jeśli pliki w kontenerze zostały utworzone przy użyciu interfejsu API REST platformy Azure zamiast systemu plików NFS, w pamięci podręcznej platformy Azure HPC są ograniczone do tych działań na oryginalnych plikach:

* Wyświetl listę plików w katalogu.
* Odczytaj plik (i przytrzymaj go w pamięci podręcznej na potrzeby kolejnych operacji odczytu).
* Usuń plik.
* Opróżnij plik (Obetnij go do wartości 0).
* Zapisz kopię pliku. Kopia jest oznaczona jako plik utworzony w systemie plików NFS i może być edytowana za pomocą systemu plików NFS.

Pamięć podręczna platformy Azure HPC **nie może** edytować zawartości pliku, który został utworzony za pomocą interfejsu REST. Oznacza to, że nie można zapisać zmienionego pliku z klienta z powrotem do miejsca docelowego magazynu.

Ważne jest zrozumienie tego ograniczenia, ponieważ może to spowodować problemy z integralnością danych w przypadku korzystania z buforowania odczytu/zapisu w plikach, które nie zostały utworzone za pomocą systemu plików NFS.

> [!TIP]
> Dowiedz się więcej na temat buforowania odczytu i zapisu w temacie [Omówienie modeli użycia pamięci podręcznej](cache-usage-models.md).

### <a name="write-caching-scenarios"></a>Scenariusze buforowania zapisu

Te modele użycia pamięci podręcznej obejmują buforowanie zapisu:

* **Ponad 15% zapisów**
* **Więcej niż 15% zapisów, sprawdzanie serwera zapasowego pod kątem zmian co 30 sekund**
* **Więcej niż 15% zapisów, sprawdzanie serwera zapasowego pod kątem zmian co 60 sekund**
* **Więcej niż 15% zapisów, zapisuj z powrotem na serwerze co 30 sekund**

Te modele użycia są używane tylko do edytowania plików utworzonych za pomocą systemu plików NFS.

Jeśli spróbujesz użyć buforowania zapisu w plikach utworzonych w usłudze REST, zmiany plików mogły zostać utracone. Wynika to z faktu, że pamięć podręczna nie próbuje natychmiast zapisać edytowanych plików w kontenerze magazynu.

Oto jak próba buforowania zapisów w plikach utworzonych w usłudze REST sprawia, że dane są zagrożone:

1. Pamięć podręczna akceptuje zmiany z klientów i zwraca komunikat o powodzeniu dla każdej zmiany.
1. Pamięć podręczna zachowuje zmieniony plik w magazynie i czeka na dodatkowe zmiany.
1. Po upływie pewnego czasu pamięć podręczna próbuje zapisać zmieniony plik w kontenerze zaplecza. W tym momencie zostanie wyświetlony komunikat o błędzie, ponieważ trwa próba zapisu do pliku utworzonego w usłudze REST przy użyciu systemu plików NFS.

   Jest zbyt późno, aby poinformować komputer kliencki, że jego zmiany nie zostały zaakceptowane, a pamięć podręczna nie ma sposobu na zaktualizowanie oryginalnego pliku. Zmiany od klientów zostaną utracone.

### <a name="read-caching-scenarios"></a>Scenariusze pamięci podręcznej odczytu

Scenariusze buforowania odczytu są odpowiednie dla plików utworzonych za pomocą systemu plików NFS lub interfejsu API REST usługi Azure Blob.

Te modele użycia używają tylko buforowania odczytu:

* **Czytaj duże, rzadko występujące zapisy**
* **Klienci zapisują w miejscu docelowym NFS, pomijając pamięć podręczną**
* **Czytaj duże i sprawdzaj serwer zapasowy co 3 godziny**

Można używać tych modeli użycia z plikami utworzonymi przez interfejs API REST lub system plików NFS. Wszystkie zapisy w systemie plików NFS wysyłane z klienta do kontenera zaplecza nadal będą kończyć się niepowodzeniem, ale natychmiast będą kończyć się niepowodzeniem i zwracają komunikat o błędzie do klienta.

Przepływ pracy pamięci podręcznej odczytu może nadal zawierać zmiany plików, o ile nie są buforowane. Na przykład klienci mogą uzyskać dostęp do plików z kontenera, ale zapisywać zmiany z powrotem jako nowy plik lub mogą zapisywać zmodyfikowane pliki w innej lokalizacji.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Rozpoznawanie ograniczeń programu Network Lock Manager (NLM)

Kontenery obiektów BLOB obsługujące system plików NFS nie obsługują usługi Network Lock Manager (NLM), która jest powszechnie używanym protokołem NFS do ochrony plików przed konfliktami.

Jeśli Twój przepływ pracy NFS został pierwotnie zapisany dla systemów magazynowania sprzętowego, aplikacje klienckie mogą obejmować żądania NLM. Aby obejść to ograniczenie podczas przenoszenia procesu do magazynu obiektów blob z obsługą systemu plików NFS, należy się upewnić, że klienci wyłączają funkcję NLM podczas instalacji pamięci podręcznej.

Aby wyłączyć NLM, użyj opcji ``-o nolock`` w poleceniu clients ``mount`` . Ta opcja uniemożliwia klientom żądanie blokady NLM i otrzymywanie błędów w odpowiedzi.

W kilku sytuacjach sam pamięć podręczna platformy Azure HPC potwierdza żądania NLM od klienta. Model użycia pamięci podręcznej o nazwie **Odczyt ciężki, rzadko** występujący zapisy potwierdza żądania NLM w imieniu magazynu zaplecza. Ten system zapobiega błędom na kliencie, ale nie tworzy w rzeczywistości blokady w kontenerze ADLS-NFS.

Jeśli przełączysz model użycia magazynu ADLS systemu plików NFS z lub z **Odczytaj duże, rzadko** występujące zapisy, należy ponownie zainstalować wszystkich klientów przy użyciu ``nolock`` opcji.

Więcej informacji o modelach NLM, pamięci podręcznej HPC i użycia znajduje się w temacie [Omówienie modeli użycia pamięci podręcznej](cache-usage-models.md#know-when-to-remount-clients-for-nlm).

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>Usprawnianie zapisu w kontenerach z obsługą systemu plików NFS za pomocą pamięci podręcznej HPC

Pamięć podręczna Azure HPC może pomóc w zwiększeniu wydajności w obciążeniu obejmującym zapisywanie zmian w miejscu docelowym magazynu ADLS-NFS.

> [!NOTE]
> Musisz użyć systemu plików NFS, aby wypełnić kontener magazynu ADLS-NFS, jeśli chcesz zmodyfikować jego pliki za pomocą pamięci podręcznej platformy Azure HPC.

Jedno z ograniczeń przedstawionych w [artykule zagadnienia dotyczące wydajności](../storage/blobs/network-file-system-protocol-support-performance.md) obiektów blob z obsługą systemu plików NFS polega na tym, że magazyn ADLS-NFS nie jest bardzo wydajny podczas zastępowania istniejących plików. Jeśli używasz pamięci podręcznej platformy Azure HPC z magazynem obiektów BLOB zainstalowanym w systemie plików NFS, pamięć podręczna obsługuje sporadyczne ponowne Zapisywanie jako komputery klienckie modyfikują aktywny plik. Opóźnienie zapisania pliku do kontenera zaplecza jest ukryte dla klientów.

Należy pamiętać o ograniczeniach opisanych powyżej w temacie [wstępne ładowanie danych przy użyciu protokołu NFS](#preload-data-with-nfs-protocol).

## <a name="next-steps"></a>Następne kroki

* Informacje o [wymaganiach wstępnych dotyczących magazynu ADLS-NFS](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview)
* Tworzenie [konta magazynu obiektów blob z obsługą systemu plików NFS](../storage/blobs/network-file-system-protocol-support-how-to.md)

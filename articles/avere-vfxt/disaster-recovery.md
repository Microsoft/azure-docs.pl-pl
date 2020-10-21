---
title: Wskazówki dotyczące odzyskiwania po awarii dla avere vFXT dla platformy Azure
description: Jak chronić dane w avere vFXT dla platformy Azure przed przypadkowym usunięciem lub awarią
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 5cc4678b082aa5a4a3f90518ff8fac448f414f1a
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342250"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Wskazówki dotyczące odzyskiwania po awarii dla avere vFXT dla platformy Azure

W tym artykule opisano strategie, które chronią avere vFXT dla przepływu pracy platformy Azure i przedstawiono wskazówki dotyczące tworzenia kopii zapasowych danych, dzięki czemu można odzyskiwać przed awariami lub awariami.

Avere vFXT for Azure tymczasowo przechowuje dane w pamięci podręcznej. Dane są przechowywane długoterminowo w systemach magazynu zaplecza — lokalnych systemach sprzętowych, kontenerach usługi Azure Blob Storage lub obu.

Aby chronić przed awarią i możliwym utratą danych, należy wziąć pod uwagę następujące cztery obszary:

* Ochrona przed przestojem, jeśli usługa avere vFXT dla systemu Azure jest niedostępna
* Ochrona danych w pamięci podręcznej klastra
* Ochrona danych w magazynie sprzętu NAS zaplecza
* Ochrona danych w usłudze Azure Blob Storage w chmurze

Każdy avere vFXT dla klienta platformy Azure musi utworzyć własny plan odzyskiwania po awarii, który obejmuje plany dla tych elementów. Odporność można również utworzyć w aplikacjach używanych z klastrem vFXT. Zapoznaj się z linkami w [sekcji Następne kroki](#next-steps) , aby uzyskać pomoc.

## <a name="protect-against-downtime"></a>Ochrona przed przestojami

Nadmiarowość jest wbudowana w avere vFXT dla produktu platformy Azure:

* Klaster jest wysoce dostępny, a poszczególne węzły klastra mogą działać w trybie failover z minimalnym przerwaniem.
* Dane zmienione w pamięci podręcznej są regularnie zapisywane w przypadku podstawowych plików głównych (sprzętowych serwerów NAS lub obiektów blob platformy Azure) do długoterminowego przechowywania.

Każdy avere vFXT dla klastra platformy Azure musi znajdować się w jednej strefie dostępności, ale można użyć nadmiarowych klastrów znajdujących się w różnych strefach lub różnych regionach, aby szybko zapewnić dostęp w przypadku awarii regionalnej.

Możesz również umieścić kontenery magazynu w wielu regionach, jeśli chcesz utracić dostęp do danych. Należy jednak pamiętać, że transakcje między regionami mają większe opóźnienia i wyższy koszt niż transakcje pozostające w regionie.

## <a name="protect-data-in-the-cluster-cache"></a>Ochrona danych w pamięci podręcznej klastra

Dane przechowywane w pamięci podręcznej są zawsze zapisywane do podstawowych plików przed regularnym zamknięciem, ale w przypadku niekontrolowanego zamknięcia można utracić zmienione dane w pamięci podręcznej.

Jeśli klaster jest używany do optymalizowania tylko odczytu plików, zmiany nie zostaną utracone. Jeśli klaster jest również używany do zmiany plików w pamięci podręcznej (zapis), należy rozważyć, czy należy dostosować podstawowe [zasady pamięci podręcznej](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) plików.<!-- link to legacy doc --> Aby dostosować, jak często dane są zapisywane do magazynu długoterminowego.

Ogólnie rzecz biorąc, plan odzyskiwania powinien skupić się na tworzeniu kopii zapasowych systemów magazynowania zaplecza, które zawierają więcej danych i zwykle są ważniejsze do ponownego ustanowienia przepływu pracy po awarii.

## <a name="protect-data-in-nas-core-filers"></a>Ochrona danych w plikach podstawowych plików NAS

Użyj zaakceptowanych metod do ochrony danych przechowywanych w lokalnym pliku sprzętowym serwera NAS, w tym migawek i pełnych kopii zapasowych zgodnie z zaleceniami dostawcy serwera NAS. Odzyskiwanie po awarii dla tych podstawowych plików wykracza poza zakres tego artykułu.

## <a name="protect-data-in-azure-blob-storage"></a>Ochrona danych w usłudze Azure Blob Storage

Program avere vFXT for Azure używa magazynu lokalnie nadmiarowego (LRS) dla plików podstawowych obiektów blob platformy Azure. Oznacza to, że dane w kontenerach obiektów BLOB są automatycznie kopiowane do ochrony przed przejściowymi awariami sprzętowymi w centrum danych.

W tej sekcji podano wskazówki dotyczące dalszej ochrony danych w usłudze BLOB Storage z rzadkich awarii w całym regionie lub przypadkowych usunięć.

Najlepsze rozwiązania dotyczące ochrony danych w usłudze Azure Blob Storage obejmują:

* Często Kopiuj krytyczne dane do innego konta magazynu w innym regionie (tak często, jak to jest określone w planie odzyskiwania po awarii).
* Kontroluj dostęp do danych we wszystkich systemach docelowych, aby zapobiec przypadkowemu usunięciu lub uszkodzeniu. Rozważ użycie [blokad zasobów](../azure-resource-manager/management/lock-resources.md) w magazynie danych.
* Włącz funkcję avere vFXT dla usługi Azure [Cloud Snapshot](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) dla plików podstawowych obiektów BLOB.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Skopiuj dane podstawowego pliku avere vFXT do konta kopii zapasowej

Wykonaj następujące kroki, aby utworzyć kopię zapasową danych na innym koncie.

1. W razie konieczności Wygeneruj nowy klucz szyfrowania i Zapisz go bezpiecznie poza systemami, których to dotyczy.

   Jeśli dane są szyfrowane przez avere vFXT dla klastra platformy Azure, należy wygenerować nowy klucz szyfrowania przed skopiowaniem danych na inne konto magazynu. Bezpiecznie przechowuj klucz i hasło w bezpiecznym obiekcie i nie będzie to miało wpływu na regionalną awarię.

   Ten klucz należy podać podczas dodawania kontenera do klastra — nawet w przypadku ponownego dodawania go do oryginalnego klastra.

   Odczytaj [Ustawienia szyfrowania chmury](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> Aby uzyskać szczegółowe informacje.

   Jeśli kontener używa tylko wbudowanego szyfrowania platformy Azure, możesz pominąć ten krok.

1. Usuń podstawowy plik z systemu. Powoduje to wymuszenie zapisania przez klaster wszystkich zmienionych danych w magazynie zaplecza.

   Mimo że trzeba będzie ponownie dodać podstawowy plik programu po utworzeniu kopii zapasowej, usunięcie go jest najlepszym sposobem na zagwarantowanie, że wszystkie dane są całkowicie zapisywane na zapleczu. (Opcja "Wstrzymaj" może czasami pozostawiać zmienione dane w pamięci podręcznej). <!-- xxx true? or just metadata? -->

   Zanotuj nazwę podstawowego pliku i informacje o rozgałęzieniu (wymienione na stronie **przestrzeni nazw** w panelu sterowania), aby można było je replikować po ponownym dodaniu kontenera po utworzeniu kopii zapasowej.

   Użyj panelu sterowania klastra, aby usunąć podstawowy plik. [Otwórz Panel sterowania klastra](avere-vfxt-cluster-gui.md) i wybierz **podstawowy**plik  >  **zarządzający**plików. Znajdź system magazynu, którego kopię zapasową chcesz utworzyć, a następnie usuń go z klastra przy użyciu przycisku **Usuń** .

1. Utwórz nowy, pusty kontener magazynu obiektów BLOB na innym koncie magazynu w innym regionie.

1. Użyj dowolnego wygodnego narzędzia do kopiowania do skopiowania danych z podstawowego pliku do nowego kontenera. Kopia musi replikować dane bez zmian i bez zakłócania własnościowego formatu systemu plików w chmurze używanego przez program avere vFXT for Azure. Narzędzia oparte na platformie Azure obejmują [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)i [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Po skopiowaniu danych do kontenera kopii zapasowych należy dodać oryginalny kontener z powrotem do klastra zgodnie z opisem w sekcji [Konfigurowanie magazynu](avere-vfxt-add-storage.md).

   * Użyj tej samej podstawowej nazwy pliku i informacji o rozgałęzieniu, aby nie trzeba było zmieniać przepływów pracy klientów.
   * Ustaw wartość **zawartości zasobnika** na istniejącą opcję dane.
   * Jeśli kontener został zaszyfrowany przez klaster, należy wprowadzić bieżący klucz szyfrowania dla jego zawartości. (Jest to klucz, który został zaktualizowany w kroku 1).

W przypadku kopii zapasowych po pierwszej, nie trzeba tworzyć nowego kontenera magazynu. Należy jednak rozważyć wygenerowanie nowego klucza szyfrowania przy każdym wykonaniu kopii zapasowej, aby upewnić się, że bieżący klucz jest przechowywany w miejscu, które pamiętasz.

### <a name="access-a-backup-data-source-during-an-outage"></a>Dostęp do źródła danych kopii zapasowej podczas awarii

Aby uzyskać dostęp do kontenera kopii zapasowych z avere vFXT dla klastra platformy Azure, wykonaj następujące czynności:

1. W razie potrzeby utwórz nowy avere vFXT dla klastra platformy Azure w nieodpowiednim regionie.

   > [!TIP]
   > Podczas tworzenia avere vFXT dla klastra platformy Azure można zapisać kopię szablonu i parametrów jego tworzenia. Jeśli zapiszesz te informacje podczas tworzenia klastra podstawowego, możesz użyć go do utworzenia zastępczego klastra z tymi samymi właściwościami. Na stronie [Podsumowanie](avere-vfxt-deploy.md#validation-and-purchase) kliknij link **Pobierz szablon i parametry** . Przed utworzeniem klastra Zapisz informacje w pliku.

1. Dodaj nowy plik w chmurze, który wskazuje na zduplikowany kontener obiektów BLOB.

   Upewnij się, że kontener docelowy zawiera już dane w ustawieniu **zawartość zasobnika** Kreatora tworzenia pliku podstawowego. (System powinien otrzymywać alerty, jeśli przypadkowo pozostawiono to ustawienie **puste**).  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. W razie potrzeby zaktualizuj klientów tak, aby instalował nowy klaster lub nowy plik podstawowy zamiast oryginalnego. (Jeśli do oryginalnego kontenera zostanie dodany zastępczy plik z taką samą nazwą i ścieżką rozgałęzienia), nie trzeba aktualizować procesów klienta, chyba że trzeba zainstalować nowy klaster pod nowym adresem IP.)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat dostosowywania ustawień dla avere vFXT dla platformy Azure, zobacz [dostrajanie klastra](avere-vfxt-tuning.md).
* Dowiedz się więcej na temat odzyskiwania po awarii i tworzenia odpornych aplikacji na platformie Azure:

  * [Odporność platformy Azure — wskazówki techniczne](/azure/architecture/framework/resiliency/overview)
  * [Odzyskiwanie po obejmujących cały region zakłóceniach usługi](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Odzyskiwanie aplikacji platformy Azure po awarii i ich wysoka dostępność](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->
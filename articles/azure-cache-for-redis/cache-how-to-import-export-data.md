---
title: Importowanie i eksportowanie danych w pamięci podręcznej platformy Azure dla Redis
description: Dowiedz się, jak importować i eksportować dane do i z magazynu obiektów BLOB za pomocą pamięci podręcznej systemu Azure w warstwie Premium dla wystąpień Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 9ee3b447b2b5f6dfa8972749c3c46ae01f79bfdc
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327512"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importowanie i eksportowanie danych w pamięci podręcznej platformy Azure dla Redis
Import/Export to pamięć podręczna platformy Azure do Redis operacji zarządzania danymi, która umożliwia importowanie danych do usługi Azure cache dla Redis lub eksportowanie danych z pamięci podręcznej platformy Azure dla Redis przez zaimportowanie i eksportowanie pamięci podręcznej platformy Azure dla migawki bazy danych Redis Database (RDB) z pamięci podręcznej Premium do obiektu BLOB na koncie usługi Azure Storage.

- **Eksportowanie** — możesz wyeksportować pamięć podręczną platformy Azure dla Redis RDB do obiektu BLOB typu Page.
- **Import** — możesz zaimportować pamięć podręczną platformy Azure dla Redis RDB z obiektu BLOB lub blokowego obiektu BLOB.

Usługa Import/Export umożliwia Migrowanie między różnymi pamięciami podręcznymi platformy Azure dla wystąpień Redis lub wypełnianie pamięci podręcznej danymi przed użyciem.

Ten artykuł zawiera Przewodnik dotyczący importowania i eksportowania danych za pomocą usługi Azure cache for Redis i zawiera odpowiedzi na często zadawane pytania.

> [!IMPORTANT]
> Usługa Import/Export jest dostępna tylko w przypadku pamięci podręcznych [warstwy Premium](cache-overview.md#service-tiers) .
>
>

## <a name="import"></a>Import
Import może służyć do przenoszenia zgodnych plików RDB Redis z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, w tym Redis działających w systemie Linux, Windows lub dowolnym dostawcy chmury, takim jak Amazon Web Services i inne. Importowanie danych to prosty sposób tworzenia pamięci podręcznej z wstępnie wypełnionymi danymi. Podczas procesu importowania usługa Azure cache for Redis ładuje pliki RDB z usługi Azure Storage do pamięci, a następnie wstawia klucze do pamięci podręcznej.

> [!NOTE]
> Przed rozpoczęciem operacji importowania upewnij się, że plik i pliki bazy danych Redis Database (RDB) są przekazywane do stron lub blokowych obiektów BLOB w usłudze Azure Storage w tym samym regionie i w ramach subskrypcji, w której znajduje się wystąpienie usługi Azure cache for Redis. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z usługą Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md). Jeśli plik RDB został wyeksportowany przy użyciu funkcji [eksportu usługi Azure cache for Redis](#export) , plik RDB jest już przechowywany w obiekcie blob stronicowania i jest gotowy do zaimportowania.
>
>

1. Aby zaimportować co najmniej jeden wyeksportowany obiekt BLOB pamięci podręcznej, [Przejdź do pamięci podręcznej](cache-configure.md#configure-azure-cache-for-redis-settings) w Azure Portal a następnie kliknij pozycję **Importuj dane** w **menu zasób**.

    ![Importowanie danych](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Kliknij pozycję **Wybierz obiekty blob** i wybierz konto magazynu zawierające dane do zaimportowania.

    ![Wybierz konto magazynu](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Kliknij kontener zawierający dane do zaimportowania.

    ![Wybieranie kontenera](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Wybierz co najmniej jeden obiekt BLOB do zaimportowania, klikając obszar z lewej strony nazwy obiektu BLOB, a następnie kliknij przycisk **Wybierz**.

    ![Wybierz obiekty blob](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Kliknij przycisk **Importuj** , aby rozpocząć proces importowania.

   > [!IMPORTANT]
   > Pamięć podręczna nie jest dostępna dla klientów pamięci podręcznej podczas procesu importowania, a wszystkie istniejące dane w pamięci podręcznej zostaną usunięte.
   >
   >

    ![Import](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Możesz monitorować postęp operacji importowania, wykonując powiadomienia z Azure Portal lub wyświetlając zdarzenia w [dzienniku inspekcji](../azure-resource-manager/management/view-activity-logs.md).

    ![Postęp importowania](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Eksportowanie
Eksport pozwala wyeksportować dane przechowywane w pamięci podręcznej platformy Azure dla Redis do Redis zgodnych plików RDB. Ta funkcja służy do przenoszenia danych z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego lub do innego serwera Redis. Podczas eksportowania plik tymczasowy jest tworzony na maszynie wirtualnej, która obsługuje wystąpienie serwera usługi Azure cache for Redis, i plik zostanie przekazany do wskazanego konta magazynu. Po zakończeniu operacji eksportowania ze stanem powodzenie lub niepowodzenie plik tymczasowy zostanie usunięty.

1. Aby wyeksportować bieżącą zawartość pamięci podręcznej do magazynu, [Przejdź do pamięci podręcznej](cache-configure.md#configure-azure-cache-for-redis-settings) w Azure Portal a następnie kliknij pozycję **Eksportuj dane** w **menu zasób**.

    ![W okienku nawigacji dla contoso5premium opcja Eksportuj dane na liście Administracja zostanie wyróżniona.](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Kliknij pozycję **Wybierz kontener magazynu** i wybierz odpowiednie konto magazynu. Konto magazynu musi znajdować się w tej samej subskrypcji i regionie co pamięć podręczna.

   > [!IMPORTANT]
   > Eksport działa ze stronicowymi obiektami BLOB, które są obsługiwane przez klasyczne i Menedżer zasobów konta magazynu, ale nie są w tej chwili obsługiwane przez konta usługi BLOB Storage. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../storage/common/storage-account-overview.md).
   >

    ![Konto magazynu](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Wybierz żądany kontener obiektów blob, a następnie kliknij przycisk **Wybierz**. Aby użyć nowego kontenera, kliknij przycisk **Dodaj kontener** , aby go najpierw dodać, a następnie wybierz go z listy.

    ![W przypadku kontenerów dla contoso55 opcja + Container jest wyróżniona. Na liście znajduje się jeden kontener, cachesaves i jest on zaznaczony i wyróżniony. Opcja zaznaczania jest zaznaczona i wyróżniona.](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Wpisz **prefiks nazwy obiektu BLOB** , a następnie kliknij przycisk **Eksportuj** , aby rozpocząć proces eksportowania. Prefiks nazwy obiektu BLOB jest używany do tworzenia prefiksu nazw plików wygenerowanych przez tę operację eksportowania.

    ![Eksportowanie](./media/cache-how-to-import-export-data/cache-export-data.png)

    Możesz monitorować postęp operacji eksportowania, wykonując powiadomienia z Azure Portal lub wyświetlając zdarzenia w [dzienniku inspekcji](../azure-resource-manager/management/view-activity-logs.md).

    ![Ukończono eksportowanie danych](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Pamięć podręczna pozostaje dostępna do użycia podczas procesu eksportu.

## <a name="importexport-faq"></a>Często zadawane pytania dotyczące importowania/eksportowania
Ta sekcja zawiera często zadawane pytania dotyczące funkcji Import/Export.

* [Jakie warstwy cenowe mogą używać importu/eksportu?](#what-pricing-tiers-can-use-importexport)
* [Czy mogę zaimportować dane z dowolnego serwera Redis?](#can-i-import-data-from-any-redis-server)
* [Jakie wersje RDB mogę zaimportować?](#what-rdb-versions-can-i-import)
* [Czy moja pamięć podręczna jest dostępna podczas operacji importowania/eksportowania?](#is-my-cache-available-during-an-importexport-operation)
* [Czy można używać importu/eksportu z klastrem Redis?](#can-i-use-importexport-with-redis-cluster)
* [W jaki sposób Usługa Import/Export współpracuje z niestandardowymi bazami danych?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Jak import/eksport różni się od trwałości Redis?](#how-is-importexport-different-from-redis-persistence)
* [Czy można zautomatyzować importowanie/eksportowanie przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Podczas operacji importu/eksportu otrzymałem błąd limitu czasu. Co to znaczy?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Wystąpił błąd podczas eksportowania danych do usługi Azure Blob Storage. Co się stało?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Jakie warstwy cenowe mogą używać importu/eksportu?
Usługa Import/Export jest dostępna tylko w warstwie cenowej Premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Czy mogę zaimportować dane z dowolnego serwera Redis?
Tak, oprócz importowania danych wyeksportowanych z pamięci podręcznej platformy Azure dla wystąpień Redis można zaimportować pliki RDB z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, na przykład w systemie Linux, Windows lub w przypadku dostawców chmury, takich jak Amazon Web Services. W tym celu Przekaż plik RDB z żądanego serwera Redis na stronę lub blokowy obiekt BLOB na koncie usługi Azure Storage, a następnie zaimportuj go do pamięci podręcznej systemu Azure w warstwie Premium dla wystąpienia Redis. Na przykład możesz chcieć wyeksportować dane z pamięci podręcznej produkcyjnej i zaimportować je do pamięci podręcznej używanej w środowisku przejściowym do testowania lub migracji.

> [!IMPORTANT]
> Aby pomyślnie zaimportować dane wyeksportowane z serwerów Redis innych niż pamięć podręczna platformy Azure dla Redis przy użyciu obiektu BLOB strony, rozmiar obiektu BLOB strony musi być wyrównany na granicy 512 bajtów. Aby uzyskać przykładowy kod do wykonania dowolnego wymaganego uzupełnienia bajtów, zobacz [przykładowe przekazywanie obiektów BLOB na stronie](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Jakie wersje RDB mogę zaimportować?

Pamięć podręczna systemu Azure dla usługi Redis obsługuje import RDB w wersji 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Czy moja pamięć podręczna jest dostępna podczas operacji importowania/eksportowania?
* Pamięć podręczna **eksportu** pozostaje dostępna i można nadal korzystać z pamięci podręcznej podczas operacji eksportowania.
* Pamięć podręczna **importu** staje się niedostępna po rozpoczęciu operacji importowania i stanie się dostępna do użycia po zakończeniu operacji importowania.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Czy można używać importu/eksportu z klastrem Redis?
Tak, i można importować/eksportować między klastrowaną pamięcią podręczną i nieklastrowaną pamięcią podręczną. Ponieważ klaster Redis [obsługuje tylko bazę danych 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), żadne dane z baz danych innych niż 0 nie są importowane. W przypadku zaimportowania klastrowanych danych pamięci podręcznej klucze są rozpowszechniane między fragmentów klastra.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>W jaki sposób Usługa Import/Export współpracuje z niestandardowymi bazami danych?
Niektóre warstwy cenowe mają różne [limity baz danych](cache-configure.md#databases), dlatego podczas importowania należy wziąć pod uwagę, jeśli skonfigurowano wartość niestandardową dla `databases` Ustawienia podczas tworzenia pamięci podręcznej.

* Podczas importowania do warstwy cenowej z niższym `databases` limitem niż warstwa, z której zostało wyeksportowane:
  * Jeśli używasz domyślnej liczby `databases` , która jest 16 dla wszystkich warstw cenowych, żadne dane nie zostaną utracone.
  * Jeśli używasz niestandardowej liczby `databases` , która znajduje się w granicach dla warstwy, do której importujesz, żadne dane nie zostaną utracone.
  * Jeśli wyeksportowane dane zawierały dane w bazie danych, które przekraczają limity nowej warstwy, nie są importowane dane z tych wyższych baz danych.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Jak import/eksport różni się od trwałości Redis?
Pamięć podręczna systemu Azure dla trwałości Redis umożliwia utrwalanie danych przechowywanych w usłudze Redis w usłudze Azure Storage. W przypadku skonfigurowania trwałości usługa Azure cache for Redis utrzymuje migawkę pamięci podręcznej platformy Azure dla Redis w formacie binarnym Redis na dysku w oparciu o konfigurowalną częstotliwość tworzenia kopii zapasowych. W przypadku wystąpienia krytycznego zdarzenia, które powoduje wyłączenie zarówno podstawowej, jak i pamięci podręcznej repliki, dane pamięci podręcznej są przywracane automatycznie przy użyciu najnowszej migawki. Aby uzyskać więcej informacji, zobacz [jak skonfigurować trwałość danych dla pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-persistence.md).

Usługa Import/Export umożliwia przenoszenie lub eksportowanie danych z pamięci podręcznej platformy Azure dla Redis. Nie konfiguruje ono tworzenia kopii zapasowych i przywracania przy użyciu trwałości Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Czy można zautomatyzować importowanie/eksportowanie przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania?
Tak, aby uzyskać instrukcje dotyczące programu PowerShell, zobacz [Importowanie pamięci podręcznej platformy Azure dla usługi Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) i [Eksportowanie pamięci podręcznej platformy Azure dla Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Podczas operacji importu/eksportu otrzymałem błąd limitu czasu. Co to oznacza?
Jeśli pozostaniesz w bloku **Importuj** dane lub **Eksportuj dane** dłużej niż 15 minut przed zainicjowaniem operacji, wystąpi błąd z komunikatem o błędzie podobnym do poniższego przykładu:

```output
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

Aby rozwiązać ten problem, zainicjuj operację importowania lub eksportowania przed upływem 15 minut.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Wystąpił błąd podczas eksportowania danych do usługi Azure Blob Storage. Co się stało?
Eksport działa tylko w przypadku plików RDB przechowywanych jako stronicowe obiekty blob. Inne typy obiektów BLOB nie są obecnie obsługiwane, w tym konta magazynu obiektów blob z warstwami gorąca i chłodna. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

* [Pamięć podręczna platformy Azure dla warstw usług Redis](cache-overview.md#service-tiers)
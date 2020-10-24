---
title: Zmienianie sposobu replikowania konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak zmienić sposób replikowania danych z istniejącego konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/24/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a16199891c00e2b8133aebebd1eaa6488423896c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487895"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Zmienianie sposobu replikowania konta magazynu

Usługa Azure Storage zawsze przechowuje wiele kopii danych w taki sposób, aby była chroniona przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętowymi, siecią lub przestojem, a także ogromnymi klęskami żywiołowymi. Nadmiarowość gwarantuje, że konto magazynu spełnia warunki [umowy dotyczącej poziomu usług (SLA) dla usługi Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) , nawet w przypadku awarii.

Usługa Azure Storage oferuje następujące typy replikacji:

- Magazyn lokalnie nadmiarowy (LRS)
- Magazyn strefowo nadmiarowy (ZRS)
- Magazyn Geograficznie nadmiarowy (GRS) lub magazyn Geograficznie nadmiarowy z dostępem do odczytu (RA-GRS)
- Geograficznie nadmiarowy magazyn (GZRS) lub geograficznie nadmiarowy dostęp do odczytu (RA-GZRS)

Aby zapoznać się z omówieniem każdej z tych opcji, zobacz [nadmiarowość usługi Azure Storage](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Przełączanie między typami replikacji

Można przełączyć konto magazynu z jednego typu replikacji do dowolnego innego typu, ale niektóre scenariusze są bardziej proste niż inne. Jeśli chcesz dodać lub usunąć replikację geograficzną lub dostęp do odczytu do regionu pomocniczego, można użyć narzędzia Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure w celu zaktualizowania ustawienia replikacji. Jeśli jednak chcesz zmienić sposób replikowania danych w regionie podstawowym, przechodząc od LRS do ZRS lub odwrotnie, należy przeprowadzić migrację ręczną.

Poniższa tabela zawiera omówienie sposobu przełączania poszczególnych typów replikacji na inne:

| Włączanie | ... do LRS | ... do GRS/RA-GRS | ... do ZRS | ... do GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... z LRS</b> | Nie dotyczy | Zmienianie ustawienia replikacji za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia<sup>1</sup> | Przeprowadź migrację ręczną <br /><br /> LUB <br /><br /> Żądaj migracji na żywo | Przeprowadź migrację ręczną <br /><br /> LUB <br /><br /> Najpierw przejdź do GRS/RA-GRS, a następnie Zażądaj migracji na żywo<sup>1</sup> |
| <b>... z GRS/RA-GRS</b> | Zmienianie ustawienia replikacji za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia | Nie dotyczy | Przeprowadź migrację ręczną <br /><br /> LUB <br /><br /> Najpierw przejdź do LRS, a następnie Zażądaj migracji na żywo | Przeprowadź migrację ręczną <br /><br /> LUB <br /><br /> Żądaj migracji na żywo |
| <b>... z ZRS</b> | Przeprowadź migrację ręczną | Przeprowadź migrację ręczną | Nie dotyczy | Użyj Azure Portal, PowerShell lub interfejsu wiersza polecenia, aby zmienić ustawienie replikacji<sup>1, 2</sup> |
| <b>... z GZRS/RA-GZRS</b> | Przeprowadź migrację ręczną | Przeprowadź migrację ręczną | Zmienianie ustawienia replikacji za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia | Nie dotyczy |

<sup>1</sup> powoduje naliczenie jednorazowej opłaty za ruch wychodzący.<br />
<sup>2</sup> konwersja z ZRS na GZRS/RA-GZRS lub odwrotnie nie jest obsługiwana w następujących regionach: Wschodnie stany USA 2, Wschodnie stany USA, Europa Zachodnia.

> [!CAUTION]
> W przypadku przełączenia w [tryb failover konta](storage-disaster-recovery-guidance.md) dla konta usługi (Ra-) GRS lub (Ra-) GZRS konto jest lokalnie nadmiarowy w nowym regionie podstawowym po przejściu do trybu failover. Migracja na żywo do ZRS lub GZRS dla konta LRS, które wynika z trybu failover, nie jest obsługiwana. Jest to prawdziwe nawet w przypadku tego typu operacji powrotu po awarii. Na przykład w przypadku przełączenia w tryb failover konta z usługi RA-GZRS do LRS w regionie pomocniczym, a następnie skonfigurowania go ponownie w usłudze RA-GRS i przełączenia w tryb failover do oryginalnego regionu podstawowego nie można skontaktować się z pomocą techniczną dla oryginalnej migracji na żywo do urzędu RA-GZRS w regionie podstawowym. Zamiast tego należy przeprowadzić migrację ręczną do ZRS lub GZRS.

## <a name="change-the-replication-setting"></a>Zmień ustawienie replikacji

Przy użyciu interfejsu wiersza polecenia Azure Portal, PowerShell lub platformy Azure można zmienić ustawienie replikacji dla konta magazynu, o ile nie zmienia się sposób replikowania danych w regionie podstawowym. W przypadku migrowania z programu LRS w regionie podstawowym do ZRS w regionie podstawowym lub na odwrót, należy przeprowadzić migrację ręczną lub migrację na żywo.

Zmiana sposobu replikowania konta magazynu nie powoduje wyłączenia aplikacji.

# <a name="portal"></a>[Portal](#tab/portal)

Aby zmienić opcję nadmiarowości dla konta magazynu w Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz ustawienie **konfiguracji** .
1. Zaktualizuj ustawienie **replikacji** .

![Zrzut ekranu przedstawiający sposób zmiany opcji replikacji w portalu](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zmienić opcję nadmiarowości dla konta magazynu za pomocą programu PowerShell, wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) i określ `-SkuName` parametr:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zmienić opcję nadmiarowości dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) i określ `--sku` parametr:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Wykonaj ręczną migrację do ZRS, GZRS lub RA-GZRS

Jeśli chcesz zmienić sposób replikowania danych z konta magazynu w regionie podstawowym, przechodząc od LRS do ZRS lub odwrotnie, można wybrać migrację ręczną. Migracja ręczna zapewnia większą elastyczność niż migracja na żywo. Możesz kontrolować chronometraż migracji ręcznej, dlatego użyj tej opcji, jeśli chcesz przeprowadzić migrację w określonym dniu.

W przypadku przeprowadzania ręcznej migracji z LRS do ZRS w regionie podstawowym lub na odwrót, docelowe konto magazynu może być Geograficznie nadmiarowy i można je również skonfigurować do odczytu regionu pomocniczego. Na przykład można migrować konto LRS do konta usługi GZRS lub RA-GZRS w jednym kroku.

Migracja ręczna może skutkować przestojem aplikacji. Jeśli aplikacja wymaga wysokiej dostępności, firma Microsoft udostępnia również opcję migracji na żywo. Migracja na żywo to migracja w miejscu bez przestojów.

W przypadku ręcznej migracji dane z istniejącego konta magazynu są kopiowane do nowego konta magazynu, które używa ZRS w regionie podstawowym. Aby przeprowadzić migrację ręczną, można użyć jednej z następujących opcji:

- Skopiuj dane przy użyciu istniejącego narzędzia, takiego jak AzCopy, jednej z bibliotek klienta usługi Azure Storage lub niezawodnego narzędzia innej firmy.
- Jeśli znasz już usługi Hadoop lub HDInsight, możesz dołączyć do klastra zarówno konto magazynu źródłowego, jak i docelowe konto magazynu. Następnie zrównoleglanie proces kopiowania danych za pomocą narzędzia, takiego jak pomocą distcp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Zażądaj migracji na żywo do ZRS, GZRS lub RA-GZRS

Jeśli musisz przeprowadzić migrację konta magazynu z usługi LRS do ZRS w regionie podstawowym bez przestojów aplikacji, możesz zażądać migracji na żywo od firmy Microsoft. Aby przeprowadzić migrację z LRS do GZRS lub RA-GZRS, najpierw Przełącz się do GRS lub RA-GRS, a następnie Zażądaj migracji na żywo. Podobnie można zażądać migracji na żywo z GRS lub RA-GRS do GZRS lub RA-GZRS. Aby przeprowadzić migrację z usługi GRS lub RA-GRS do usługi ZRS, najpierw Przełącz się do LRS, a następnie Zażądaj migracji na żywo.

Podczas migracji na żywo możesz uzyskać dostęp do danych na koncie magazynu bez utraty trwałości ani dostępności. Umowa SLA usługi Azure Storage jest utrzymywana podczas procesu migracji. Brak utraty danych skojarzonej z migracją na żywo. Punkty końcowe usługi, klucze dostępu, sygnatury dostępu współdzielonego i inne opcje konta pozostają bez zmian po migracji.

ZRS obsługuje tylko konta ogólnego przeznaczenia w wersji 2, dlatego pamiętaj, aby uaktualnić konto magazynu przed przesłaniem żądania migracji na żywo do ZRS. Aby uzyskać więcej informacji, zobacz [uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](storage-account-upgrade.md). Konto magazynu musi zawierać dane, które mają zostać zmigrowane za pośrednictwem migracji na żywo.

Migracja na żywo jest obsługiwana tylko w przypadku kont magazynu, które używają replikacji LRS lub GRS. Jeśli Twoje konto używa RA-GRS, przed kontynuowaniem musisz najpierw zmienić typ replikacji konta na LRS lub GRS. Ten krok pośrednika usuwa pomocniczy punkt końcowy tylko do odczytu dostarczony przez RA-GRS przed migracją.

Mimo że firma Microsoft obsłuży Twoje żądanie migracji na żywo tak szybko, jak to możliwe, nie ma możliwości określenia, kiedy migracja na żywo zostanie ukończona. Jeśli chcesz, aby migracja Twoich danych została zakończona w określonym terminie, firma Microsoft zaleca przeprowadzenie zamiast tego migracji ręcznej. Ogólnie rzecz biorąc, im więcej danych zawiera Twoje konto, tym dłużej trwa ich migracja.

Należy przeprowadzić migrację ręczną, jeśli:

- Chcesz przeprowadzić migrację danych do konta magazynu ZRS, które znajduje się w regionie innym niż konto źródłowe.
- Twoje konto magazynu jest obiektem BLOB strony Premium lub blokowym kontem BLOB.
- Chcesz migrować dane z ZRS do LRS, GRS lub RA-GRS.
- Twoje konto magazynu zawiera dane w warstwie archiwum.

Możesz zażądać migracji na żywo za pomocą [portalu pomocy technicznej systemu Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). W portalu wybierz konto magazynu, które chcesz skonwertować do ZRS.

1. Wybierz pozycję **nowe żądanie obsługi**.
2. Wypełnij **podstawowe** informacje na podstawie informacji o koncie: 
    - **Typ problemu**: wybierz pozycję **techniczne**.
    - **Usługa**: wybierz pozycję **Moje usługi** i **Zarządzanie kontem magazynu**.
    - **Zasób**: wybierz zasób, który chcesz przekonwertować na ZRS.
3. Wybierz pozycję **Dalej**.
4. Określ następujące wartości w sekcji **problem** :
    - **Ważność**: pozostaw wartość domyślną równą-is.
    - **Typ problemu**: wybierz pozycję **migracja danych**.
    - **Kategoria**: wybierz pozycję **Migruj do ZRS**.
    - **Title**: wpisz opisowy tytuł, na przykład **ZRS**.
    - **Szczegóły**: wpisz dodatkowe szczegóły w polu **szczegóły** , na przykład chcę przeprowadzić migrację do ZRS z [LRS, GRS] w \_ \_ regionie.
5. Wybierz pozycję **Dalej**.
6. Sprawdź, czy informacje kontaktowe są poprawne w bloku **informacje kontaktowe** .
7. Wybierz przycisk **Utwórz**.

Osoba odpowiedzialna za pomoc techniczną skontaktuje się z Tobą i pomoże Ci uzyskać pomoc.

> [!NOTE]
> Migracja na żywo nie jest obecnie obsługiwana dla udziałów plików w warstwie Premium. Obecnie obsługiwane są tylko ręczne kopiowanie lub przeniesienie danych.
>
> Konta magazynu GZRS nie obsługują obecnie warstwy archiwum. Aby uzyskać więcej informacji [, zobacz warstwy dostępu gorąca, chłodna i archiwalna](/azure/storage/blobs/storage-blob-storage-tiers) .
>
> Dyski zarządzane są dostępne tylko dla LRS i nie można ich migrować do ZRS. Można przechowywać migawki i obrazy dla dysków zarządzanych przy standardowych dyskach SSD w standardowym magazynie DYSKowym i [wybierać między opcjami LRS i ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Aby uzyskać informacje na temat integracji z zestawami dostępności, zobacz [wprowadzenie do usługi Azure Managed disks](/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Przełącz z ZRS klasycznego

> [!IMPORTANT]
> Firma Microsoft będzie wycofać i zmigrować klasyczne konta ZRS w dniu 31 marca 2021. Więcej szczegółowych informacji ZRS klasycznym klientom przed jego wycofaniem.
>
> Gdy ZRS stanie się ogólnie dostępna w danym regionie, klienci nie będą już mogli tworzyć klasycznych kont ZRS z Azure Portal w tym regionie. Za pomocą programu Microsoft PowerShell i interfejsu wiersza polecenia platformy Azure do tworzenia kont klasycznych ZRS jest opcja do momentu, w którym jest przestarzałe ZRS klasyczne. Aby uzyskać informacje o tym, gdzie ZRS jest dostępny, zobacz [nadmiarowość usługi Azure Storage](storage-redundancy.md).

ZRS klasyczny asynchronicznie replikuje dane między centrami danych w jednym lub dwóch regionach. Zreplikowane dane mogą nie być dostępne, jeśli firma Microsoft zainicjuje przejście w tryb failover do pomocniczej. Nie można przekonwertować klasycznego konta ZRS na lub z LRS, GRS lub RA-GRS. Klasyczne konta ZRS również nie obsługują metryk ani rejestrowania.

ZRS klasyczny jest dostępny tylko dla **blokowych obiektów BLOB** w ramach kont magazynu ogólnego przeznaczenia w wersji 1 (GPv1). Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage](storage-account-overview.md).

Aby ręcznie przeprowadzić migrację danych konta ZRS do lub z klasycznego konta LRS, GRS, RA-GRS lub ZRS, użyj jednego z następujących narzędzi: AzCopy, Eksplorator usługi Azure Storage, PowerShell lub interfejsu wiersza polecenia platformy Azure. Możesz również utworzyć własne rozwiązanie migracji przy użyciu jednej z bibliotek klienckich usługi Azure Storage.

Możesz również uaktualnić konto magazynu ZRS klasycznego do ZRS przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure w regionach, w których ZRS jest dostępny.

# <a name="portal"></a>[Portal](#tab/portal)

Aby uaktualnić do ZRS w Azure Portal, przejdź do ustawień **konfiguracji** konta i wybierz pozycję **Uaktualnij**:

![Uaktualnianie klasycznego magazynu ZRS do magazynu ZRS w portalu](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby uaktualnić program do ZRS przy użyciu programu PowerShell, wywołaj następujące polecenie:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uaktualnić do ZRS przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj następujące polecenie:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Koszty związane ze zmianą sposobu replikowania danych

Koszty związane ze zmianą sposobu replikowania danych zależą od ścieżki konwersji. Porządkowanie od najniższych do najtańszych ofert nadmiarowości usługi Azure Storage obejmuje LRS, ZRS, GRS, RA-GRS, GZRS i RA-GZRS.

Na przykład przechodzenie *z* LRS do dowolnego innego typu replikacji wiąże się z dodatkowymi opłatami, ponieważ przenosisz do bardziej zaawansowanego poziomu nadmiarowości. Migrowanie *do* GRS lub RA-GRS spowoduje naliczenie opłaty za przepustowość ruchu wychodzącego, ponieważ dane (w regionie podstawowym) są replikowane do zdalnego regionu pomocniczego. Ta opłata jest naliczana jednorazowo podczas początkowej konfiguracji. Po skopiowaniu danych nie ma żadnych dodatkowych opłat związanych z migracją. Aby uzyskać szczegółowe informacje o opłatach za przepustowość, zobacz [stronę z cennikiem usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Jeśli przeprowadzisz migrację konta magazynu z usługi GRS do LRS, nie ma żadnych dodatkowych kosztów, ale zreplikowane dane są usuwane z lokalizacji pomocniczej.

> [!IMPORTANT]
> Jeśli przeprowadzono migrację konta magazynu z usługi RA-GRS do usługi GRS lub LRS, to konto jest rozliczane jako RA-GRS przez dodatkowe 30 dni poza datą przekonwertowania.

## <a name="see-also"></a>Zobacz też

- [Nadmiarowość usługi Azure Storage](storage-redundancy.md)
- [Sprawdź Właściwość godzina ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md)
- [Projektowanie aplikacji o wysokiej dostępności przy użyciu nadmiarowości geograficznej](geo-redundant-design.md)

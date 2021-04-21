---
title: Zmienianie sposobu replikowania konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak zmienić sposób replikowania danych na istniejącym koncie magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eb8bbf852803df53c43cef90bd2229bfcddd60d4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766191"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Zmienianie sposobu replikowania konta magazynu

Usługa Azure Storage zawsze przechowuje wiele kopii danych, dzięki czemu są chronione przed planowanymi i nieplanowanych zdarzeniami, w tym przejściowymi awariami sprzętu, awariami sieci lub zasilania oraz olbrzymimi klęskami żywiołowymi. Nadmiarowość zapewnia, że konto magazynu spełnia warunki umowy dotyczącej poziomu usług [(SLA)](https://azure.microsoft.com/support/legal/sla/storage/) dla usługi Azure Storage nawet w przypadku awarii.

Usługa Azure Storage oferuje następujące typy replikacji:

- Magazyn lokalnie nadmiarowy (LRS)
- Magazyn strefowo nadmiarowy (ZRS)
- Magazyn geograficznie nadmiarowy (GRS) lub magazyn geograficznie nadmiarowy z dostępem do odczytu (RA-GRS)
- Magazyn geograficznie i strefowo nadmiarowy (GZRS) lub magazyn geograficznie i strefowo nadmiarowy z dostępem do odczytu (RA-GZRS)

Aby uzyskać omówienie każdej z tych opcji, zobacz [Nadmiarowość usługi Azure Storage.](storage-redundancy.md)

## <a name="switch-between-types-of-replication"></a>Przełączanie między typami replikacji

Konto magazynu można przełączyć z jednego typu replikacji na inny typ, ale niektóre scenariusze są prostsze niż inne. Jeśli chcesz dodać lub usunąć replikację geograficzną albo dostęp do odczytu do regionu pomocniczego, możesz zaktualizować ustawienie replikacji przy użyciu programu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Jeśli jednak chcesz zmienić sposób replikowania danych w regionie podstawowym, przechodząc z LRS do ZRS lub odwrotnie, musisz przeprowadzić migrację ręczną.

W poniższej tabeli przedstawiono omówienie sposobu przełączania z poszczególnych typów replikacji na inny:

| Przełączania | ... do LRS | ... do GRS/RA-GRS | ... do ZRS | ... do GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... z LRS</b> | Nie dotyczy | Użyj Azure Portal, programu PowerShell lub interfejsu wiersza polecenia, aby zmienić ustawienie replikacji<sup>1,2</sup> | Przeprowadzanie migracji ręcznej <br /><br /> LUB <br /><br /> Żądanie migracji na żywo | Przeprowadzanie migracji ręcznej <br /><br /> LUB <br /><br /> Najpierw przejdź do grs/RA-GRS, a następnie zażądaj migracji na żywo<sup>1</sup> |
| <b>... z grs/RA-GRS</b> | Użyj Azure Portal, programu PowerShell lub interfejsu wiersza polecenia, aby zmienić ustawienie replikacji | Nie dotyczy | Przeprowadzanie migracji ręcznej <br /><br /> LUB <br /><br /> Najpierw przejdź do LRS, a następnie zażądaj migracji na żywo | Przeprowadzanie migracji ręcznej <br /><br /> LUB <br /><br /> Żądanie migracji na żywo |
| <b>... z ZRS</b> | Przeprowadzanie migracji ręcznej | Przeprowadzanie migracji ręcznej | Nie dotyczy | Żądanie migracji na żywo |
| <b>... z GZRS/RA-GZRS</b> | Przeprowadzanie migracji ręcznej | Przeprowadzanie migracji ręcznej | Użyj Azure Portal, programu PowerShell lub interfejsu wiersza polecenia, aby zmienić ustawienie replikacji | Nie dotyczy |

<sup>1</sup> Naliczana jest opłata za jeden ruch wychodzący.<br />
<sup>2</sup> Migracja z magazynu LRS do magazynu GRS nie jest obsługiwana, jeśli konto magazynu zawiera obiekty blob w warstwie Archiwum.<br />
<sup>3 Konwersja</sup> z ZRS na GZRS/RA-GZRS lub odwrotnie nie jest obsługiwana w następujących regionach: Wschodnie stany USA 2, Wschodnie stany USA, Europa Zachodnia.

> [!CAUTION]
> W przypadku wykonania trybu [failover](storage-disaster-recovery-guidance.md) konta (RA-)GRS lub (RA-)GZRS konto jest lokalnie nadmiarowe (LRS) w nowym regionie podstawowym po zakończeniu trybu failover. Migracja na żywo do magazynu ZRS lub GZRS dla konta magazynu LRS wynikającego z trybu failover nie jest obsługiwana. Jest to prawdziwe nawet w przypadku tak zwanych operacji powrotu po awarii. Na przykład w przypadku wykonania trybu failover konta z magazynu RA-GZRS do magazynu LRS w regionie pomocniczym, a następnie skonfigurowania go ponownie do magazynu RA-GRS i wykonania trybu failover innego konta do oryginalnego regionu podstawowego, nie można skontaktować się z pomocą techniczną dla oryginalnej migracji na żywo do magazynu RA-GZRS w regionie podstawowym. Zamiast tego należy przeprowadzić ręczną migrację do usług ZRS lub GZRS.

## <a name="change-the-replication-setting"></a>Zmienianie ustawienia replikacji

Ustawienia replikacji dla konta magazynu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure można zmienić, o ile nie zmieniasz sposobu replikowania danych w regionie podstawowym. Jeśli przeprowadzasz migrację z usługi LRS w regionie podstawowym do ZRS w regionie podstawowym lub odwrotnie, musisz przeprowadzić migrację ręczną lub migrację na żywo.

Zmiana sposobu replikowania konta magazynu nie powoduje czasu pracy aplikacji.

# <a name="portal"></a>[Portal](#tab/portal)

Aby zmienić opcję nadmiarowości konta magazynu w Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz **ustawienie** Konfiguracja.
1. Zaktualizuj ustawienie **Replikacja.**

![Zrzut ekranu przedstawiający sposób zmiany opcji replikacji w portalu](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zmienić opcję nadmiarowości konta magazynu przy użyciu programu PowerShell, wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) i określ `-SkuName` parametr:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zmienić opcję nadmiarowości konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage account update](/cli/azure/storage/account#az_storage_account_update) i określ `--sku` parametr:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Przeprowadzanie ręcznej migracji do ZRS, GZRS lub RA-GZRS

Jeśli chcesz zmienić sposób replikowania danych na koncie magazynu w regionie podstawowym, przechodząc z magazynu LRS do magazynu ZRS lub odwrotnie, możesz zdecydować się na przeprowadzenie migracji ręcznej. Migracja ręczna zapewnia większą elastyczność niż migracja na żywo. Możesz kontrolować czas migracji ręcznej, więc użyj tej opcji, jeśli chcesz, aby migracja była ukończona w określonym dniu.

W przypadku ręcznej migracji z magazynu LRS do magazynu ZRS w regionie podstawowym lub na odwrót docelowe konto magazynu może być geograficznie nadmiarowe i można je również skonfigurować do odczytu do regionu pomocniczego. Na przykład w jednym kroku można przeprowadzić migrację konta magazynu LRS do konta magazynu GZRS lub RA-GZRS.

Ręczna migracja może spowodować przestój aplikacji. Jeśli aplikacja wymaga wysokiej dostępności, firma Microsoft udostępnia również opcję migracji na żywo. Migracja na żywo to migracja w miejscu bez przestojów.

W przypadku migracji ręcznej dane z istniejącego konta magazynu są kopiowane do nowego konta magazynu, które używa magazynu ZRS w regionie podstawowym. Aby przeprowadzić migrację ręczną, można użyć jednej z następujących opcji:

- Kopiowanie danych przy użyciu istniejącego narzędzia, takiego jak AzCopy, jedna z bibliotek klienta usługi Azure Storage lub niezawodne narzędzie innej firmy.
- Jeśli znasz usługę Hadoop lub HDInsight, możesz dołączyć do klastra źródłowe konto magazynu i docelowe konto magazynu. Następnie zrównoleglij proces kopiowania danych za pomocą narzędzia takiego jak DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Żądanie migracji na żywo do usług ZRS, GZRS lub RA-GZRS

Jeśli musisz przeprowadzić migrację konta magazynu z magazynu LRS do magazynu ZRS w regionie podstawowym bez przestoju aplikacji, możesz zażądać migracji na żywo od firmy Microsoft. Aby przeprowadzić migrację z LRS do GZRS lub RA-GZRS, najpierw przełącz się do grs lub RA-GRS, a następnie zażądaj migracji na żywo. Podobnie można zażądać migracji na żywo z grs lub RA-GRS do GZRS lub RA-GZRS. Aby przeprowadzić migrację z grs lub RA-GRS do ZRS, najpierw przełącz się do LRS, a następnie zażądaj migracji na żywo.

Podczas migracji na żywo można uzyskać dostęp do danych na koncie magazynu bez utraty trwałości ani dostępności. Umowa SLA usługi Azure Storage jest zachowywana podczas procesu migracji. Migracja na żywo nie wiąże się z utratą danych. Punkty końcowe usługi, klucze dostępu, sygnatury dostępu współdzielone i inne opcje konta pozostają niezmienione po migracji.

Magazyn ZRS obsługuje tylko konta ogólnego przeznaczenia w wersji 2, dlatego pamiętaj o uaktualnieniu konta magazynu przed przesłaniem żądania migracji na żywo do magazynu ZRS. Aby uzyskać więcej informacji, zobacz [Upgrade to a general-purpose v2 storage account (Uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2).](storage-account-upgrade.md) Konto magazynu musi zawierać dane, które mają zostać zmigrowane za pośrednictwem migracji na żywo.

Migracja na żywo jest obsługiwana tylko w przypadku kont magazynu, które korzystają z replikacji LRS lub GRS. Jeśli twoje konto używa magazynu RA-GRS, przed podjęciem pracy musisz najpierw zmienić typ replikacji konta na LRS lub GRS. Ten krok pośredniczy usuwa pomocniczy punkt końcowy tylko do odczytu dostarczony przez usługę RA-GRS przed migracją.

Mimo że firma Microsoft obsłuży Twoje żądanie migracji na żywo tak szybko, jak to możliwe, nie ma możliwości określenia, kiedy migracja na żywo zostanie ukończona. Jeśli chcesz, aby migracja Twoich danych została zakończona w określonym terminie, firma Microsoft zaleca przeprowadzenie zamiast tego migracji ręcznej. Ogólnie rzecz biorąc, im więcej danych zawiera Twoje konto, tym dłużej trwa ich migracja.

Migrację ręczną należy przeprowadzić, jeśli:

- Chcesz przeprowadzić migrację danych na konto magazynu ZRS, które znajduje się w regionie innym niż konto źródłowe.
- Konto magazynu jest kontem stronicowego obiektu blob lub blokowego obiektu blob w chmurze.
- Chcesz przeprowadzić migrację danych z usług ZRS do LRS, GRS lub RA-GRS.
- Twoje konto magazynu zawiera dane w warstwie Archiwum.

Migrację na żywo można zażądać za [pośrednictwem pomoc techniczna platformy Azure portal.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 

> [!IMPORTANT]
> Jeśli musisz przeprowadzić migrację więcej niż jednego konta magazynu, utwórz pojedynczy bilet pomocy technicznej i określ nazwy kont do konwersji na **karcie Szczegóły.**

Wykonaj następujące kroki, aby zażądać migracji na żywo:

1. W Azure Portal przejdź do konta magazynu, które chcesz zmigrować.
1. W **obszarze Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję Nowe żądanie **obsługi.**
1. Wypełnij **kartę Podstawowe** na podstawie informacji o koncie:
    - **Typ problemu:** wybierz pozycję **Techniczny.**
    - **Usługa:** wybierz **pozycję Moje usługi,** a następnie **pozycję Zarządzanie kontami magazynu.**
    - **Zasób:** wybierz konto magazynu do migracji. Jeśli musisz określić wiele kont magazynu, możesz to zrobić w **sekcji** Szczegóły.
    - **Typ problemu:** wybierz **pozycję Migracja danych.**
    - **Podtyp problemu:** wybierz pozycję **Migruj do ZRS, GZRS lub RA-GZRS.**

    :::image type="content" source="media/redundancy-migration/request-live-migration-basics-portal.png" alt-text="Zrzut ekranu przedstawiający żądanie migracji na żywo — karta Podstawy":::

1. Wybierz opcję **Dalej**. Na **karcie Rozwiązania** możesz sprawdzić uprawnienia kont magazynu do migracji.
1. Wybierz opcję **Dalej**. Jeśli masz więcej niż jedno konto magazynu  do zmigrowania, na karcie Szczegóły określ nazwy poszczególnych kont rozdzielone średnikami.

    :::image type="content" source="media/redundancy-migration/request-live-migration-details-portal.png" alt-text="Zrzut ekranu przedstawiający żądanie migracji na żywo — karta Szczegóły":::

1. Wypełnij dodatkowe wymagane informacje na karcie **Szczegóły,** a następnie wybierz pozycję **Przejrzyj i** utwórz, aby przejrzeć i przesłać bilet pomocy technicznej. Osoba odpowiedzialna za pomoc techniczną skontaktuje się z Tobą w celu zapewnienia pomocy, której możesz potrzebować.

> [!NOTE]
> Udziały plików Premium (konta FileStorage) są dostępne tylko dla magazynu LRS i ZRS.
>
> Konta magazynu GZRS nie obsługują obecnie warstwy Archiwum. Aby uzyskać więcej informacji, zobacz [Azure Blob storage: hot, cool, and archive access tiers](../blobs/storage-blob-storage-tiers.md) (Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum).
>
> Dyski zarządzane są dostępne tylko dla magazynu LRS i nie można ich migrować do magazynu ZRS. Migawki i obrazy dla dysków zarządzanych SSD w warstwie Standardowa można przechowywać w standardowym magazynie HDD i wybierać między [opcjami LRS i ZRS.](https://azure.microsoft.com/pricing/details/managed-disks/) Aby uzyskać informacje na temat integracji z zestawami dostępności, [zobacz Introduction to Azure managed disks (Wprowadzenie do dysków zarządzanych platformy Azure).](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets)

## <a name="switch-from-zrs-classic"></a>Przełączanie z klasycznego ZRS

> [!IMPORTANT]
> Firma Microsoft wycofa i zmigruje klasyczne konta magazynu ZRS 31 marca 2021 r. Przed cofdaniem wersji klasycznej usługi ZRS zostaną podane więcej szczegółów.
>
> Gdy magazynu ZRS stanie się ogólnie dostępny w danym regionie, klienci nie będą już mogli tworzyć klasycznych kont magazynu ZRS na Azure Portal w tym regionie. Tworzenie klasycznych kont magazynu ZRS przy użyciu programu Microsoft PowerShell i interfejsu wiersza polecenia platformy Azure jest opcją do momentu, gdy klasyczny magazyn ZRS zostanie wycofany. Aby uzyskać informacje o tym, gdzie jest dostępny magazyn ZRS, zobacz [Nadmiarowość usługi Azure Storage](storage-redundancy.md).

Klasyczny model ZRS asynchronicznie replikuje dane między centrami danych w jednym lub dwóch regionach. Replikowane dane mogą nie być dostępne, chyba że firma Microsoft zainicjuje trybu failover do pomocniczej bazy danych. Klasycznego konta magazynu ZRS nie można przekonwertować na lub z magazynu LRS, GRS ani RA-GRS. Klasyczne konta magazynu ZRS również nie obsługują metryk ani rejestrowania.

Klasyczny magazyn ZRS jest dostępny tylko dla **blokowych obiektów blob** na kontach magazynu ogólnego przeznaczenia w wersji 1 (GPv1). Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage.](storage-account-overview.md)

Aby ręcznie przeprowadzić migrację danych konta magazynu ZRS do lub z konta LRS, GRS, RA-GRS lub ZRS Classic, użyj jednego z następujących narzędzi: AzCopy, Eksplorator usługi Azure Storage, PowerShell lub interfejs wiersza polecenia platformy Azure. Możesz również utworzyć własne rozwiązanie do migracji za pomocą jednej z bibliotek klienta usługi Azure Storage.

Możesz również uaktualnić klasyczne konto magazynu ZRS do magazynu ZRS przy użyciu interfejsu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure w regionach, w których magazyn ZRS jest dostępny.

# <a name="portal"></a>[Portal](#tab/portal)

Aby uaktualnić do magazynu ZRS w Azure Portal, przejdź do ustawień konfiguracji konta i wybierz pozycję **Uaktualnij:** 

![Uaktualnianie klasycznego magazynu ZRS do magazynu ZRS w portalu](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby uaktualnić do ZRS przy użyciu programu PowerShell, wywołaj następujące polecenie:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uaktualnić do magazynu ZRS przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj następujące polecenie:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Koszty związane ze zmianą sposobu replikowania danych

Koszty związane ze zmianą sposobu replikowania danych zależą od ścieżki konwersji. Oferty nadmiarowości usługi Azure Storage obejmują usługi LRS, ZRS, GRS, RA-GRS, GZRS i RA-GZRS.

Na przykład przejście *z LRS* do dowolnego innego typu replikacji spowoduje naliczenie dodatkowych opłat, ponieważ przenosisz się na bardziej zaawansowany poziom nadmiarowości. Migracja do *magazynu* GRS lub RA-GRS spowoduje naliczenie opłaty za przepustowość wychodzącą, ponieważ dane (w regionie podstawowym) są replikowane do zdalnego regionu pomocniczego. Ta opłata jest kosztem tylko raz podczas wstępnej konfiguracji. Po skopiowaniu danych nie są naliczane żadne dodatkowe opłaty za migrację. Aby uzyskać szczegółowe informacje na temat opłat za przepustowość, zobacz [stronę Cennik usługi Azure Storage.](https://azure.microsoft.com/pricing/details/storage/blobs/)

W przypadku migrowania konta magazynu z magazynu GEOGRAFICZNIE do magazynu LRS nie ma dodatkowych kosztów, ale replikowane dane są usuwane z lokalizacji pomocniczej.

> [!IMPORTANT]
> W przypadku migrowania konta magazynu z magazynu RA-GRS do magazynu GRS lub LRS to konto jest rozliczane jako MAGAZYN RA-GRS przez dodatkowe 30 dni od daty konwersji.

## <a name="see-also"></a>Zobacz też

- [Nadmiarowość usługi Azure Storage](storage-redundancy.md)
- [Sprawdzanie właściwości Czas ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md)
- [Używanie nadmiarowości geograficznej do projektowania aplikacji o wysokiej dostępie](geo-redundant-design.md)

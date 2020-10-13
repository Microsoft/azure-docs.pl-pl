---
title: Inicjowanie trybu failover konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak zainicjować pracę w trybie failover w przypadku, gdy podstawowy punkt końcowy konta magazynu stał się niedostępny. Tryb failover aktualizuje region pomocniczy, aby stał się regionem podstawowym konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e39548a923e76fc118dec4158398d02577ec20c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91610062"
---
# <a name="initiate-a-storage-account-failover"></a>Inicjowanie trybu failover konta magazynu

Jeśli podstawowy punkt końcowy dla konta magazynu geograficznie nadmiarowego stanie się niedostępny z dowolnego powodu, możesz zainicjować tryb failover konta. Tryb failover konta aktualizuje pomocniczy punkt końcowy, aby stał się podstawowym punktem końcowym dla konta magazynu. Po zakończeniu pracy w trybie failover klienci mogą zacząć zapisywać w nowym regionie podstawowym. Wymuszone przejście w tryb failover pozwala zachować wysoką dostępność aplikacji.

W tym artykule pokazano, jak zainicjować tryb failover konta dla konta magazynu za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej o przejściu do trybu failover, zobacz [odzyskiwanie po awarii i konto magazynu w trybie failover](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Przejście w tryb failover na koncie zazwyczaj skutkuje utratą danych. Aby zrozumieć konsekwencje przejścia do trybu failover dla konta i przygotować się do utraty danych, zapoznaj [się z tematem proces przełączania do trybu failover dla konta](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wykonać tryb failover konta na koncie magazynu, upewnij się, że konto magazynu jest skonfigurowane pod kątem replikacji geograficznej. Twoje konto magazynu może korzystać z jednej z następujących opcji nadmiarowości:

- Magazyn Geograficznie nadmiarowy (GRS) lub magazyn Geograficznie nadmiarowy z dostępem do odczytu (RA-GRS)
- Geograficznie nadmiarowy magazyn (GZRS) lub geograficznie nadmiarowy dostęp do odczytu (RA-GZRS)

Aby uzyskać więcej informacji o nadmiarowości usługi Azure Storage, zobacz [nadmiarowość usługi Azure Storage](storage-redundancy.md).

## <a name="initiate-the-failover"></a>Inicjowanie trybu failover

## <a name="portal"></a>[Portal](#tab/azure-portal)

Aby zainicjować tryb failover konta z Azure Portal, wykonaj następujące kroki:

1. Przejdź do konta magazynu.
1. W obszarze **Ustawienia**wybierz pozycję **replikacja geograficzna**. Na poniższej ilustracji przedstawiono stan replikacji geograficznej i trybu failover dla konta magazynu.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="Zrzut ekranu przedstawiający replikację geograficzną i stan trybu failover":::

1. Sprawdź, czy konto magazynu jest skonfigurowane pod kątem magazynu geograficznie nadmiarowego (GRS) lub magazynu geograficznie nadmiarowego do odczytu (RA-GRS). Jeśli tak nie jest, wybierz pozycję **Konfiguracja** w obszarze **Ustawienia** , aby zaktualizować konto tak, aby było geograficznie nadmiarowe.
1. Właściwość **czas ostatniej synchronizacji** wskazuje, jak daleko pomocnicza jest poza podstawową. **Czas ostatniej synchronizacji** to oszacowanie zakresu utraty danych, który będzie dostępny po zakończeniu pracy w trybie failover. Aby uzyskać więcej informacji na temat sprawdzania właściwości **czas ostatniej synchronizacji** , zobacz [Sprawdzanie właściwości ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md).
1. Wybierz pozycję **Przygotuj do przejścia w tryb failover**.
1. Przejrzyj okno dialogowe potwierdzenia. Gdy wszystko będzie gotowe, wprowadź **wartość tak** , aby potwierdzić i zainicjować tryb failover.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="Zrzut ekranu przedstawiający replikację geograficzną i stan trybu failover":::

## <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Funkcja trybu failover konta jest ogólnie dostępna, ale nadal bazuje na module w wersji zapoznawczej dla programu PowerShell. Aby można było zainicjować tryb failover konta przy użyciu programu PowerShell, należy najpierw zainstalować moduł AZ. Storage [1.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) . Wykonaj następujące kroki, aby zainstalować moduł:

1. Odinstaluj wszystkie poprzednie instalacje Azure PowerShell:

    - Usuń wszystkie poprzednie instalacje Azure PowerShell z systemu Windows za pomocą ustawienia **aplikacje & funkcje** w obszarze **Ustawienia**.
    - Usuń wszystkie moduły **platformy Azure** z programu `%Program Files%\WindowsPowerShell\Modules` .

1. Upewnij się, że masz zainstalowaną najnowszą wersję programu PowerShellGet. Otwórz okno programu Windows PowerShell i uruchom następujące polecenie, aby zainstalować najnowszą wersję:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Zamknij i ponownie otwórz okno programu PowerShell po zainstalowaniu PowerShellGet.

1. Zainstaluj najnowszą wersję Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Zainstaluj moduł usługi Azure Storage w wersji zapoznawczej obsługujący tryb failover konta:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

Aby zainicjować tryb failover konta z programu PowerShell, wykonaj następujące polecenie:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zainicjować tryb failover na koncie przy użyciu interfejsu wiersza polecenia platformy Azure, wykonaj następujące polecenia:

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>Ważne konsekwencje przełączenia w tryb failover

Po zainicjowaniu konta magazynu w trybie failover rekordy DNS dla pomocniczego punktu końcowego są aktualizowane, tak aby pomocniczy punkt końcowy stał się podstawowym punktem końcowym. Przed zainicjowaniem trybu failover upewnij się, że rozumiesz potencjalny wpływ na konto magazynu.

Aby oszacować zakres prawdopodobnej utraty danych przed zainicjowaniem trybu failover, należy sprawdzić Właściwość **godzina ostatniej synchronizacji** . Aby uzyskać więcej informacji na temat sprawdzania właściwości **czas ostatniej synchronizacji** , zobacz [Sprawdzanie właściwości ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md).

Po przejściu w tryb failover typ konta magazynu zostanie automatycznie przekonwertowany na magazyn lokalnie nadmiarowy (LRS) w nowym regionie podstawowym. Można ponownie włączyć magazyn Geograficznie nadmiarowy (GRS) lub magazyn Geograficznie nadmiarowy do odczytu (RA-GRS) dla konta. Należy pamiętać, że konwersja z LRS na GRS lub RA-GRS wiąże się z dodatkowymi kosztami. Aby uzyskać dodatkowe informacje, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/)dotyczącego przepustowości.

Po ponownym włączeniu GRS dla konta magazynu firma Microsoft rozpocznie replikowanie danych z konta do nowego regionu pomocniczego. Czas replikacji zależy od ilości replikowanych danych.  

## <a name="next-steps"></a>Następne kroki

- [Odzyskiwanie po awarii i tryb failover konta magazynu](storage-disaster-recovery-guidance.md)
- [Sprawdź Właściwość godzina ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md)
- [Projektowanie aplikacji o wysokiej dostępności przy użyciu nadmiarowości geograficznej](geo-redundant-design.md)
- [Samouczek: Tworzenie aplikacji o wysokiej dostępności przy użyciu magazynu obiektów BLOB](../blobs/storage-create-geo-redundant-storage.md)

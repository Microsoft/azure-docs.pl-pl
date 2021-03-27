---
title: Inicjowanie obsługi administracyjnej puli w sieci wirtualnej
description: Jak utworzyć pulę usługi Batch w sieci wirtualnej platformy Azure, dzięki czemu węzły obliczeniowe mogą bezpiecznie komunikować się z innymi maszynami wirtualnymi w sieci, takimi jak serwer plików.
ms.topic: how-to
ms.date: 03/26/2021
ms.custom: seodec18
ms.openlocfilehash: 7213637e89cfccd1352861002c47a696d942d30f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629312"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Tworzenie puli Azure Batch w sieci wirtualnej

Podczas tworzenia puli Azure Batch można zainicjować obsługę administracyjną puli w podsieci określonej przez użytkownika [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) . W tym artykule wyjaśniono, jak skonfigurować pulę wsadową w sieci wirtualnej.

## <a name="why-use-a-vnet"></a>Dlaczego warto używać sieci wirtualnej?

Węzły obliczeniowe w puli mogą komunikować się ze sobą, na przykład do uruchamiania zadań o wiele wystąpień, bez konieczności oddzielnej sieci wirtualnej. Jednak domyślnie węzły w puli nie mogą komunikować się z maszynami wirtualnymi spoza puli, takimi jak serwery licencji lub serwery plików.

Aby umożliwić węzłom obliczeniowym bezpieczne komunikowanie się z innymi maszynami wirtualnymi lub z siecią lokalną, można zainicjować obsługę administracyjną puli w podsieci sieci wirtualnej platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- **Uwierzytelnianie**. Aby użyć sieci wirtualnej platformy Azure, interfejs API klienta usługi Batch musi korzystać z uwierzytelniania usługi Azure Active Directory (AD). Obsługa usługi Azure Batch dla usługi Azure AD jest udokumentowana w temacie [Authenticate Batch service solutions with Active Directory (Uwierzytelnianie rozwiązań usługi Batch za pomocą usługi Active Directory)](batch-aad-auth.md).

- **Sieć wirtualna platformy Azure**. Zapoznaj się z następującą sekcją dotyczącą wymagań i konfiguracji sieci wirtualnej. Aby przygotować sieć wirtualną z wyprzedzeniem z co najmniej jedną podsiecią, można użyć Azure Portal, Azure PowerShell, interfejsu Azure Command-Line Interface (CLI) lub innych metod.
  - Aby utworzyć sieć wirtualną opartą na Azure Resource Manager, zobacz [Tworzenie sieci wirtualnej](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Sieć wirtualna oparta na Menedżer zasobów jest zalecana w przypadku nowych wdrożeń i jest obsługiwana tylko w pulach, które używają konfiguracji maszyny wirtualnej.
  - Aby utworzyć klasyczną sieć wirtualną, zobacz [Tworzenie sieci wirtualnej (klasycznej) z wieloma podsieciami](/previous-versions/azure/virtual-network/create-virtual-network-classic). Klasyczna Sieć wirtualna jest obsługiwana tylko w pulach, które używają konfiguracji Cloud Services.

## <a name="vnet-requirements"></a>Wymagania dotyczące sieci wirtualnej

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Utwórz pulę z siecią wirtualną w Azure Portal

Po utworzeniu sieci wirtualnej i przypisaniu do niej podsieci można utworzyć pulę wsadową z tą siecią wirtualną. Wykonaj następujące kroki, aby utworzyć pulę na podstawie Azure Portal: 

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. To konto musi znajdować się w tej samej subskrypcji i regionie co grupa zasobów zawierająca sieć wirtualną, która ma być używana.
2. W oknie **Ustawienia** po lewej stronie wybierz element menu **Pule** .
3. W oknie **Pule** wybierz pozycję **Dodaj**.
4. W oknie **Dodawanie puli** wybierz opcję, która ma zostać użyta z listy rozwijanej **Typ obrazu** .
5. Wybierz prawidłową **wersję wydawcy/oferty/jednostki SKU** dla obrazu niestandardowego.
6. Określ pozostałe wymagane ustawienia, w tym **rozmiar węzła**, **docelowe węzły dedykowane** i **węzły o niskim priorytecie**, a także wszystkie wymagane ustawienia opcjonalne.
7. W **Virtual Network** wybierz sieć wirtualną i podsieć, której chcesz użyć.

   ![Dodawanie puli przy użyciu sieci wirtualnej](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Trasy zdefiniowane przez użytkownika dla tunelowania wymuszonego

W Twojej organizacji mogą istnieć wymagania dotyczące przekierowywania (wymuszania) ruchu związanego z Internetu z podsieci z powrotem do lokalizacji lokalnej na potrzeby inspekcji i rejestrowania. Ponadto można włączyć Wymuszone tunelowanie dla podsieci w sieci wirtualnej.

Aby upewnić się, że węzły w puli działają w sieci wirtualnej z włączonym wymuszonym tunelowaniem, należy dodać następujące [trasy zdefiniowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) (UDR) dla tej podsieci:

- Usługa Batch musi komunikować się z węzłami w celu planowania zadań. Aby włączyć tę komunikację, należy dodać UDR dla każdego adresu IP używanego przez usługę Batch w regionie, w którym istnieje konto usługi Batch. Aby uzyskać listę adresów IP usługi Batch, zobacz [lokalne znaczniki usług](../virtual-network/service-tags-overview.md).

- Upewnij się, że ruch wychodzący do usługi Azure Storage (w odniesieniu do adresów URL formularza `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` i `<account>.blob.core.windows.net` ) nie jest blokowany przez sieć lokalną.

- W przypadku korzystania z instalacji plików wirtualnych Sprawdź [wymagania dotyczące sieci](virtual-file-mount.md#networking-requirements) i upewnij się, że żaden wymagany ruch nie jest blokowany.

Po dodaniu UDR należy zdefiniować trasę dla każdego powiązanego prefiksu adresu IP partii i ustawić **Typ następnego przeskoku** na **Internet**.

![Trasa zdefiniowana przez użytkownika](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Adresy IP usługi Batch mogą ulec zmianie z upływem czasu. Aby zapobiec awariom ze względu na zmianę adresu IP, należy utworzyć proces automatycznego odświeżania adresów IP usługi Batch i zachować ich aktualność w tabeli tras.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Dowiedz się [, jak utworzyć zdefiniowaną przez użytkownika trasę w Azure Portal](../virtual-network/tutorial-create-route-table-portal.md).

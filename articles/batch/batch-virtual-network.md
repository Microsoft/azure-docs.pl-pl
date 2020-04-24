---
title: Inicjowanie obsługi administracyjnej puli w sieci wirtualnej — Azure Batch | Microsoft Docs
description: Jak utworzyć pulę usługi Batch w sieci wirtualnej platformy Azure, dzięki czemu węzły obliczeniowe mogą bezpiecznie komunikować się z innymi maszynami wirtualnymi w sieci, takimi jak serwer plików.
ms.topic: article
ms.date: 04/03/2020
ms.custom: seodec18
ms.openlocfilehash: 616118d5f75f9bfa6d97d89baac9d7ea9186cd5d
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111899"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Tworzenie puli Azure Batch w sieci wirtualnej

Podczas tworzenia puli Azure Batch można zainicjować obsługę administracyjną puli w podsieci określonej przez użytkownika [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) . W tym artykule wyjaśniono, jak skonfigurować pulę wsadową w sieci wirtualnej.

## <a name="why-use-a-vnet"></a>Dlaczego warto używać sieci wirtualnej?

Pula Azure Batch ma ustawienia umożliwiające węzłom obliczeniowym komunikowanie się ze sobą, na przykład w celu uruchamiania zadań o wiele wystąpień. Te ustawienia nie wymagają oddzielnej sieci wirtualnej. Jednak domyślnie węzły nie mogą komunikować się z maszynami wirtualnymi, które nie należą do puli partii, takich jak serwer licencji lub serwer plików. Aby umożliwić węzłom obliczeniowym puli bezpieczne komunikowanie się z innymi maszynami wirtualnymi lub z siecią lokalną, można zainicjować obsługę administracyjną puli w podsieci sieci wirtualnej platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* **Uwierzytelnianie**. Aby użyć sieci wirtualnej platformy Azure, interfejs API klienta usługi Batch musi korzystać z uwierzytelniania usługi Azure Active Directory (AD). Obsługa usługi Azure Batch dla usługi Azure AD jest udokumentowana w temacie [Authenticate Batch service solutions with Active Directory (Uwierzytelnianie rozwiązań usługi Batch za pomocą usługi Active Directory)](batch-aad-auth.md).

* **Sieć wirtualna platformy Azure**. Zapoznaj się z następującą sekcją dotyczącą wymagań i konfiguracji sieci wirtualnej. Aby przygotować sieć wirtualną z wyprzedzeniem z co najmniej jedną podsiecią, można użyć Azure Portal, Azure PowerShell, interfejsu wiersza polecenia (CLI) platformy Azure lub innych metod.
  * Aby utworzyć sieć wirtualną opartą na Azure Resource Manager, zobacz [Tworzenie sieci wirtualnej](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Sieć wirtualna oparta na Menedżer zasobów jest zalecana w przypadku nowych wdrożeń i jest obsługiwana tylko w pulach w konfiguracji maszyny wirtualnej.
  * Aby utworzyć klasyczną sieć wirtualną, zobacz [Tworzenie sieci wirtualnej (klasycznej) z wieloma podsieciami](../virtual-network/create-virtual-network-classic.md). Klasyczna Sieć wirtualna jest obsługiwana tylko w pulach w konfiguracji Cloud Services.

## <a name="vnet-requirements"></a>Wymagania dotyczące sieci wirtualnej

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Tworzenie puli przy użyciu sieci wirtualnej w portalu

Po utworzeniu sieci wirtualnej i przypisaniu do niej podsieci można utworzyć pulę wsadową z tą siecią wirtualną. Wykonaj następujące kroki, aby utworzyć pulę na podstawie Azure Portal: 

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. To konto musi znajdować się w tej samej subskrypcji i regionie co grupa zasobów zawierająca sieć wirtualną, która ma być używana.
2. W oknie **Ustawienia** po lewej stronie wybierz element menu **Pule** .
3. W oknie **Pule** wybierz polecenie **Dodaj** .
4. W oknie **Dodawanie puli** wybierz opcję, która ma zostać użyta z listy rozwijanej **Typ obrazu** .
5. Wybierz prawidłową **wersję wydawcy/oferty/jednostki SKU** dla obrazu niestandardowego.
6. Określ pozostałe wymagane ustawienia, w tym **rozmiar węzła**, **docelowe węzły dedykowane**i **węzły o niskim priorytecie**, a także wszystkie wymagane ustawienia opcjonalne.
7. W **Virtual Network**wybierz sieć wirtualną i podsieć, której chcesz użyć.

   ![Dodawanie puli przy użyciu sieci wirtualnej](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Trasy zdefiniowane przez użytkownika dla tunelowania wymuszonego

W Twojej organizacji mogą istnieć wymagania dotyczące przekierowywania (wymuszania) ruchu związanego z Internetu z podsieci z powrotem do lokalizacji lokalnej na potrzeby inspekcji i rejestrowania. Być może włączono Wymuszone tunelowanie dla podsieci w sieci wirtualnej.

Aby upewnić się, że węzły obliczeniowe puli Azure Batch działają w sieci wirtualnej z włączonym wymuszonym tunelowaniem, należy dodać następujące [trasy zdefiniowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) dla tej podsieci:

* Usługa Batch musi komunikować się z węzłami obliczeniowymi puli na potrzeby planowania zadań. Aby włączyć tę komunikację, Dodaj zdefiniowaną przez użytkownika trasę dla każdego adresu IP używanego przez usługę Batch w regionie, w którym istnieje konto usługi Batch. Aby dowiedzieć się, jak uzyskać listę adresów IP usługi Batch, zobacz [lokalne znaczniki usług](../virtual-network/service-tags-overview.md). Adresy IP usługi Batch zostaną skojarzone z tagiem `BatchNodeManagement` usługi (lub odmianą regionalną zgodną z regionem konta usługi Batch).

* Upewnij się, że ruch wychodzący do usługi Azure Storage (w `<account>.table.core.windows.net`odniesieniu do adresów URL formularza, `<account>.queue.core.windows.net`i `<account>.blob.core.windows.net`) nie jest blokowany za pośrednictwem lokalnego urządzenia sieciowego.

Po dodaniu trasy zdefiniowanej przez użytkownika Zdefiniuj trasę dla każdego powiązanego prefiksu adresu IP partii i ustaw **Typ następnego przeskoku** na **Internet**. Zobacz poniższy przykład:

![Trasa zdefiniowana przez użytkownika](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Adresy IP usługi Batch mogą ulec zmianie z upływem czasu. Aby zapobiec awarii ze względu na zmianę adresu IP, sugerujemy ustanowienie okresowego procesu odświeżania adresów IP usługi Batch automatycznie i zachować ich aktualność w tabeli tras.

## <a name="next-steps"></a>Następne kroki

- Szczegółowe omówienie usługi Batch można znaleźć w temacie [programowanie równoległych rozwiązań obliczeniowych na dużą skalę za pomocą usługi Batch](batch-api-basics.md).
- Aby uzyskać więcej informacji na temat tworzenia trasy zdefiniowanej przez użytkownika, zobacz [Tworzenie trasy zdefiniowanej przez użytkownika Azure Portal](../virtual-network/tutorial-create-route-table-portal.md).

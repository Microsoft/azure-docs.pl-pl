---
title: Zarządzanie strefami DNS w Azure DNS-Azure Portal | Microsoft Docs
description: Za pomocą Azure Portal można zarządzać strefami DNS. W tym artykule opisano, jak aktualizować, usuwać i tworzyć strefy DNS na Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 9b6cac64d2e3def673f6d7c27e80bd64eead9e97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84689167"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Jak zarządzać Strefy DNS w Azure Portal

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Program PowerShell](dns-operations-dnszones.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md)

W tym artykule pokazano, jak zarządzać strefami DNS przy użyciu Azure Portal. Strefami DNS można także zarządzać za pomocą międzyplatformowego [interfejsu wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md) lub programu Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

1. Logowanie się do witryny Azure Portal
2. W menu centrum przejdź do obszaru **Tworzenie zasobu > sieć > strefa DNS** , aby otworzyć blok **Tworzenie strefy DNS** .

    ![Strefa DNS](./media/dns-operations-dnszones-portal/openzone650.png)

4. W bloku **Tworzenie strefy DNS** wprowadź następujące wartości, a następnie kliknij pozycję **Utwórz**:


   | **Ustawienie** | **Wartość** | **Szczegóły** |
   |---|---|---|
   |**Nazwa**|contoso.com|Nazwa strefy DNS|
   |**Subskrypcja**|[Twoja subskrypcja]|Wybierz subskrypcję, aby utworzyć w jej ramach strefę DNS.|
   |**Grupa zasobów**|**Utwórz nową:** contosoDNSRG|Utwórz grupę zasobów. Nazwa grupy zasobów musi być unikatowa w obrębie wybranej subskrypcji. Aby dowiedzieć się więcej na temat grup zasobów, zapoznaj się z artykułem Omówienie [Menedżer zasobów](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) .|
   |**Lokalizacja**|Zachodnie stany USA||

> [!NOTE]
> Ustawienie Grupa zasobów dotyczy lokalizacji grupy zasobów i nie ma wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

## <a name="list-dns-zones"></a>Wyświetlanie listy stref DNS

W Azure Portal przejdź do **kolejnych usług**  >  **sieciowych**  >  **strefy DNS**. Każda strefa DNS jest własnym zasobem, a informacje takie jak liczba zestawów rekordów i serwerów nazw można wyświetlić w tym widoku. **Serwery nazw** kolumn nie są w widoku domyślnym. Aby je dodać, kliknij przycisk **kolumny**, wybierz pozycję **serwery nazw**, a następnie kliknij przycisk **gotowe**.

![Wyświetlanie listy stref DNS](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Usuwanie strefy DNS

Przejdź do strefy DNS w portalu. W bloku **strefa DNS** kliknij pozycję **Usuń strefę**. Zostanie wyświetlony monit o potwierdzenie usunięcia strefy DNS. Usunięcie strefy DNS powoduje również usunięcie wszystkich rekordów znajdujących się w strefie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak korzystać z strefy i rekordów DNS, odwiedzając [wprowadzenie do Azure DNS przy użyciu Azure Portal](dns-getstarted-portal.md).
---
title: Odzyskiwanie usuniętego konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak odzyskać usunięte konto magazynu w ramach Azure Portal.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57cd3361d7888d9d7f747955257d96282274fd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357350"
---
# <a name="recover-a-deleted-storage-account"></a>Odzyskiwanie usuniętego konta magazynu

Usunięte konto magazynu może zostać odzyskane w niektórych przypadkach z poziomu Azure Portal. Aby odzyskać konto magazynu, muszą być spełnione następujące warunki:

- Konto magazynu zostało usunięte w ciągu ostatnich 14 dni.
- Konto magazynu zostało utworzone przy użyciu modelu wdrażania Azure Resource Manager.
- Nowe konto magazynu o tej samej nazwie nie zostało utworzone, ponieważ oryginalne konto zostało usunięte.

Przed podjęciem próby odzyskania usuniętego konta magazynu upewnij się, że istnieje grupa zasobów dla tego konta. Jeśli grupa zasobów została usunięta, należy ją utworzyć ponownie. Nie jest możliwe odzyskanie grupy zasobów. Aby uzyskać więcej informacji na temat, zobacz [Zarządzanie grupami zasobów](../../azure-resource-manager/management/manage-resource-groups-portal.md).

Jeśli usunięte konto magazynu używało kluczy zarządzanych przez klienta z usługą Azure Key Vault i Magazyn kluczy został również usunięty, należy przywrócić magazyn kluczy przed przywróceniem konta magazynu. Aby uzyskać więcej informacji, zobacz [Omówienie odzyskiwania Azure Key Vault](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> Odzyskiwanie usuniętego konta magazynu nie jest gwarantowane. Odzyskiwanie jest najlepszą próbą. Firma Microsoft zaleca blokowanie zasobów, aby zapobiec przypadkowemu usunięciu konta. Więcej informacji o blokadach zasobów znajduje się w temacie [blokowanie zasobów w celu zapobiegania zmianom](../../azure-resource-manager/management/lock-resources.md).
>
> Kolejną najlepszym rozwiązaniem, aby uniknąć przypadkowego usunięcia konta, jest ograniczenie liczby użytkowników, którzy mają uprawnienia do usuwania konta za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące usługi Azure RBAC](../../role-based-access-control/best-practices.md).

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Odzyskaj usunięte konto z Azure Portal

Aby odzyskać usunięte konto magazynu z poziomu innego konta magazynu, wykonaj następujące czynności:

1. Przejdź do strony przegląd dla istniejącego konta magazynu w Azure Portal.
1. W sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **Odzyskaj usunięte konto**.
1. Z listy rozwijanej wybierz konto do odzyskania, jak pokazano na poniższej ilustracji. Jeśli konto magazynu, które chcesz odzyskać, nie znajduje się na liście rozwijanej, nie można go odzyskać.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Zrzut ekranu przedstawiający sposób odzyskiwania konta magazynu w Azure Portal":::

1. Wybierz przycisk **Odzyskaj** , aby przywrócić konto. W portalu zostanie wyświetlone powiadomienie, że odzyskiwanie jest w toku.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>Odzyskiwanie usuniętego konta za pośrednictwem biletu pomocy technicznej

1. W Azure Portal przejdź do **pomocy i obsługi technicznej**.
1. Wybierz pozycję **Nowe żądanie obsługi**.
1. Na karcie **podstawowe** w polu **typ problemu** wybierz pozycję **techniczne**.
1. W polu **subskrypcja** wybierz subskrypcję zawierającą usunięte konto magazynu.
1. W polu **Usługa** wybierz pozycję **Zarządzanie kontem magazynu**.
1. W polu **zasób** wybierz dowolny zasób konta magazynu. Usunięte konto magazynu nie zostanie wyświetlone na liście.
1. Dodaj krótkie podsumowanie problemu.
1. W polu **typ problemu** wybierz pozycję **usuwanie i odzyskiwanie**.
1. W polu **podtyp problemu** wybierz opcję **Odzyskaj usunięte konto magazynu**. Na poniższej ilustracji przedstawiono przykład wypełnionej karty **podstawowe** :

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="Zrzut ekranu przedstawiający sposób odzyskiwania konta magazynu za pomocą karty informacje o biletach pomocy technicznej":::

1. Następnie przejdź do karty **rozwiązania** , a następnie wybierz pozycję **Odzyskiwanie konta magazynu kontrolowanego przez klienta**, jak pokazano na poniższej ilustracji:

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="Zrzut ekranu przedstawiający sposób odzyskiwania konta magazynu za pomocą karty rozwiązania biletów pomocy technicznej":::

1. Z listy rozwijanej wybierz konto do odzyskania, jak pokazano na poniższej ilustracji. Jeśli konto magazynu, które chcesz odzyskać, nie znajduje się na liście rozwijanej, nie można go odzyskać.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="Zrzut ekranu przedstawiający sposób odzyskiwania konta magazynu za pomocą biletu pomocy technicznej":::

1. Wybierz przycisk **Odzyskaj** , aby przywrócić konto. W portalu zostanie wyświetlone powiadomienie, że odzyskiwanie jest w toku.

## <a name="next-steps"></a>Następne kroki

- [Omówienie kont magazynu](storage-account-overview.md)
- [Tworzenie konta magazynu](storage-account-create.md)
- [Uaktualnienie konta magazynu ogólnego przeznaczenia do wersji 2](storage-account-upgrade.md)
- [Przenoszenie konta usługi Azure Storage do innego regionu](storage-account-move.md)
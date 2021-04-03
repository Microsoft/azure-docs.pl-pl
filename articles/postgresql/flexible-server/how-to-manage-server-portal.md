---
title: Zarządzanie serwerem Azure Portal-Azure Database for PostgreSQL-elastycznym
description: Dowiedz się, jak zarządzać serwerem elastycznym Azure Database for PostgreSQL z Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91961412"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Zarządzanie serwerem elastycznym Azure Database for PostgreSQL przy użyciu Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

W tym artykule opisano sposób zarządzania serwerem elastycznym Azure Database for PostgreSQL. Zadania zarządzania obejmują skalowanie obliczeniowe i magazynowe, Resetowanie hasła administratora oraz wyświetlanie szczegółów serwera.

## <a name="sign-in"></a>Zaloguj

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Przejdź do elastycznego zasobu serwera w Azure Portal.

## <a name="scale-compute-and-storage"></a>Skalowanie zasobów obliczeniowych i magazynu

Po utworzeniu serwera można skalować różne [warstwy cenowe](https://azure.microsoft.com/pricing/details/postgresql/) odpowiednio do potrzeb. Możesz również skalować w górę lub w dół zasoby obliczeniowe i pamięć, zwiększając lub zmniejszając liczbę rdzeni wirtualnych.

> [!NOTE]
> Nie można skalować magazynu w dół do niższej wartości.

1. Wybierz serwer w Azure Portal. Wybierz pozycję **obliczeniowe + magazyn**, znajdujący się w sekcji **Ustawienia** .
2. Można zmienić **warstwę obliczeniową** **rdzeń wirtualny**, **Magazyn** w celu skalowania w górę serwera przy użyciu wyższego poziomu obliczeniowego lub skalowania w górę w ramach tej samej warstwy przez zwiększenie magazynu lub rdzeni wirtualnych na żądaną wartość.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="skalowanie serwera elastycznego magazynu":::

> [!Important]
> - Nie można skalować magazynu.
> - Skalowanie rdzeni wirtualnych powoduje ponowne uruchomienie serwera.

3. Wybierz **przycisk OK** , aby zapisać zmiany.

## <a name="reset-admin-password"></a>Zresetuj hasło administratora

Hasło roli administratora można zmienić przy użyciu Azure Portal.

1. Wybierz serwer w Azure Portal. W oknie **Przegląd** wybierz pozycję **Resetuj hasło**.
2. Wprowadź nowe hasło i Potwierdź hasło. W polu tekstowym zostanie wyświetlony monit o podanie wymagań dotyczących złożoności haseł.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="Zresetuj hasło dla serwera elastycznego":::

3. Wybierz pozycję **Zapisz** , aby zapisać nowe hasło.

## <a name="delete-a-server"></a>Usuwanie serwera

Serwer można usunąć, jeśli nie jest już potrzebny.

1. Wybierz serwer w Azure Portal. W oknie **Przegląd** wybierz pozycję **Usuń**.
2. Wpisz nazwę serwera w polu wejściowym, aby potwierdzić, że chcesz usunąć serwer.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="Usuń elastyczny serwer":::

   > [!IMPORTANT]
   > Usuwanie serwera jest nieodwracalne.

  > [!div class="mx-imgBorder"]
  > ![Usuń elastyczny serwer](./media/howto-manage-server-portal/delete-server.png)  

3. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- [Informacje o pojęciach dotyczących tworzenia kopii zapasowych i przywracania](concepts-backup-restore.md)
- [Dostrajanie i monitorowanie serwera](concepts-monitoring.md)

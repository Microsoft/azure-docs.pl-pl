---
title: Zarządzanie serwerem Azure Portal-Azure Database for MySQL elastycznym
description: Dowiedz się, jak zarządzać Azure Database for MySQL elastycznym serwerze z Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937329"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Zarządzanie serwerem elastycznym Azure Database for MySQL (wersja zapoznawcza) przy użyciu Azure Portal


> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

W tym artykule pokazano, jak zarządzać serwerami elastycznymi Azure Database for MySQL (wersja zapoznawcza). Zadania zarządzania obejmują skalowanie obliczeniowe i magazynowe, hasło administratora serwera Rest oraz usuwanie serwera.

## <a name="sign-in"></a>Zaloguj
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Przejdź do elastycznego zasobu serwera w Azure Portal.

## <a name="scale-compute-and-storage"></a>Skalowanie zasobów obliczeniowych i magazynu

Po utworzeniu serwera można skalować różne [warstwy cenowe](https://azure.microsoft.com/pricing/details/mysql/) odpowiednio do potrzeb. Możesz również skalować w górę lub w dół zasoby obliczeniowe i pamięć, zwiększając lub zmniejszając rdzeni wirtualnych.

1. Wybierz serwer w Azure Portal. Wybierz pozycję **obliczeniowe + magazyn**, znajdujący się w sekcji **Ustawienia** .

2. Można zmienić **warstwę obliczeniową** **rdzeń wirtualny**, **Magazyn** w celu skalowania w górę serwera przy użyciu wyższego poziomu obliczeń lub skalowania w górę w ramach tej samej warstwy przez zwiększenie magazynu lub rdzeni wirtualnych na żądaną wartość.

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
   > :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="skalowanie serwera elastycznego magazynu":::

3. Wybierz pozycję **Zapisz** , aby zapisać nowe hasło.

## <a name="delete-a-server"></a>Usuwanie serwera

Serwer można usunąć, jeśli nie jest już potrzebny.

1. Wybierz serwer w Azure Portal. W oknie **Przegląd** wybierz pozycję **Usuń**.

2. Wpisz nazwę serwera w polu wejściowym, aby potwierdzić, że chcesz usunąć serwer.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="skalowanie serwera elastycznego magazynu":::

   > [!NOTE]
   > Usuwanie serwera jest nieodwracalne.

3. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się, jak uruchomić lub zatrzymać serwer](how-to-stop-start-server-portal.md)
- [Dowiedz się, jak przywrócić serwer](how-to-restore-server-portal.md)
- [Rozwiązywanie problemów z połączeniem](how-to-troubleshoot-common-connection-issues.md)


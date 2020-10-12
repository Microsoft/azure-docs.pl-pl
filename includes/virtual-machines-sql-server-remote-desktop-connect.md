---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: fe5daa38c43723c85fb464e191ee4a3e85700e0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183187"
---
1. Po utworzeniu i uruchomieniu maszyny wirtualnej platformy Azure kliknij ikonę Maszyny wirtualne w witrynie Azure Portal, aby wyświetlić swoje maszyny wirtualne.

1. Kliknij przycisk wielokropka (**... **) dla nowej maszyny wirtualnej.

1. Kliknij przycisk **Połącz**.

   ![Nawiązywanie połączenia z maszyną wirtualną w portalu](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. Otwórz plik **RDP**, który przeglądarka pobiera dla maszyny wirtualnej.

1. Funkcja Podłączanie pulpitu zdalnego powiadomi, że nie można zidentyfikować wydawcy tego połączenia zdalnego. Kliknij pozycję **Połącz**, aby kontynuować.

1. W oknie dialogowym **Zabezpieczenia systemu Windows** kliknij pozycję **Użyj innego konta**. Może być konieczne kliknięcie pozycji **Więcej opcji**, aby ją wyświetlić. Podaj nazwę użytkownika i hasło, które zostały skonfigurowane podczas tworzenia maszyny wirtualnej. Musisz dodać ukośnik odwrotny przed nazwą użytkownika.

   ![Uwierzytelnianie pulpitu zdalnego](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

1. Kliknij przycisk **OK**, aby nawiązać połączenie.

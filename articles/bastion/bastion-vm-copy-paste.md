---
title: 'Kopiowanie i wklejanie do i z maszyny wirtualnej: Azure bastionu'
description: W tym artykule dowiesz się, jak kopiować i wklejać do i z maszyny wirtualnej platformy Azure przy użyciu programu bastionu.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: charwen
ms.openlocfilehash: 8580b7e28c8a4860739efa0763ebb7c20a80585e
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780287"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Kopiowanie i wklejanie do maszyny wirtualnej: Azure bastionu

Ten artykuł pomaga w kopiowaniu i wklejaniu tekstu do i z maszyn wirtualnych podczas korzystania z usługi Azure bastionu. Przed rozpoczęciem pracy z maszyną wirtualną upewnij się, że wykonano kroki umożliwiające [utworzenie hosta bastionu](bastion-create-host-portal.md). Następnie nawiąż połączenie z maszyną wirtualną, z którą chcesz współpracować przy użyciu [protokołu RDP](bastion-connect-vm-rdp.md) lub [SSH](bastion-connect-vm-ssh.md).

W przypadku przeglądarek obsługujących zaawansowany dostęp do interfejsu API schowka można kopiować i wklejać tekst między urządzeniem lokalnym a sesją zdalną w taki sam sposób, jak kopiowanie i wklejanie między aplikacjami na urządzeniu lokalnym. W przypadku innych przeglądarek można użyć palety narzędzia dostępu do schowka bastionu.

>[!NOTE]
>Tylko kopiowanie/wklejanie tekstu jest obecnie obsługiwane.
>

   ![Zezwalaj na schowek](./media/bastion-vm-manage/allow.png)

Obsługiwane jest tylko kopiowanie tekstu/wklejanie. W przypadku bezpośredniej kopiowania i wklejania przeglądarka może monitować o dostęp do schowka po zainicjowaniu sesji bastionu. **Zezwól** stronie sieci Web na dostęp do Schowka.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Kopiuj do sesji zdalnej

Po nawiązaniu połączenia z maszyną wirtualną przy użyciu [Azure Portal ](https://portal.azure.com)wykonaj następujące czynności:

1. Skopiuj tekst/zawartość z urządzenia lokalnego do schowka lokalnego.
1. Podczas sesji zdalnej Uruchom paletę narzędzi dostępu do schowka bastionu, wybierając dwie strzałki. Strzałki znajdują się na lewym środku sesji.

   ![Paleta narzędzi](./media/bastion-vm-manage/left.png)

   ![schowek](./media/bastion-vm-manage/clipboard.png)
1. Zwykle kopiowany tekst jest automatycznie wyświetlany na palecie kopiowania bastionu. Jeśli Twój tekst nie istnieje, wklej tekst w obszarze tekstu na palecie.
1. Gdy tekst znajduje się w obszarze tekstu, można wkleić go do sesji zdalnej.

   ![Wklej](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Kopiuj z sesji zdalnej

Po nawiązaniu połączenia z maszyną wirtualną przy użyciu [Azure Portal ](https://portal.azure.com)wykonaj następujące czynności:

1. Skopiuj tekst/zawartość z sesji zdalnej do zdalnego schowka (przy użyciu kombinacji klawiszy CTRL-C).

   ![Paleta narzędzi](./media/bastion-vm-manage/remote.png)
1. Podczas sesji zdalnej Uruchom paletę narzędzi dostępu do schowka bastionu, wybierając dwie strzałki. Strzałki znajdują się na lewym środku sesji.

   ![schowek](./media/bastion-vm-manage/clipboard2.png)
1. Zwykle kopiowany tekst jest automatycznie wyświetlany na palecie kopiowania bastionu. Jeśli Twój tekst nie istnieje, wklej tekst w obszarze tekstu na palecie.
1. Gdy tekst znajduje się w obszarze tekstu, można wkleić go do urządzenia lokalnego.

   ![Wklej](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [bastionu często zadawanych pytań](bastion-faq.md).

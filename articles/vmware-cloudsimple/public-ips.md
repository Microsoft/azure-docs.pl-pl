---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Przydziel publiczne adresy IP
description: Opisuje sposób przydzielania publicznych adresów IP maszynom wirtualnym w środowisku chmury prywatnej
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bb46ad726cd3b99324e9bb96b998ed1b4da885de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899188"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Przydzielanie publicznych adresów IP dla środowiska chmury prywatnej

Aby przydzielić publiczne adresy IP maszyn wirtualnych w środowisku chmury prywatnej, Otwórz na stronie sieć kartę publiczna IP.

1. [Uzyskaj dostęp do portalu CloudSimple](access-cloudsimple-portal.md) i wybierz pozycję **Sieć** w menu po stronie.
2. Wybierz pozycję **publiczne adresy IP**.
3. Kliknij pozycję **Nowy publiczny adres IP**.

    ![Strona publiczne adresy IP](media/public-ips-page.png)

4. Wprowadź nazwę identyfikującą wpis adresu IP.
5. Zachowaj lokalizację domyślną.
6. Użyj suwaka, aby zmienić limit czasu bezczynności, jeśli jest to konieczne.
7. Wprowadź lokalny adres IP, dla którego chcesz przypisać publiczny adres IP.
8. Wprowadź skojarzoną nazwę DNS.
9. Kliknij przycisk **Prześlij**.

![Przydziel publiczne adresy IP](media/network-public-ip-allocate.png)

Zostanie rozpoczęte zadanie alokowania publicznego adresu IP. Stan zadania można sprawdzić na stronie **zadania > zadań** . Po zakończeniu alokacji nowy wpis zostanie wyświetlony na stronie publiczne adresy IP.

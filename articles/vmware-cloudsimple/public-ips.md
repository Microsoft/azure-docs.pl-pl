---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Przydziel publiczne adresy IP
description: Opisuje sposób przydzielania publicznych adresów IP maszynom wirtualnym w środowisku chmury prywatnej
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77024300"
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

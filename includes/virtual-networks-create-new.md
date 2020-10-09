---
title: plik dołączania
description: plik dołączania
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78244981"
---
## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną**wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **Utwórz nowy**, wprowadź **\<resource-group-name>** , a następnie wybierz pozycję OK lub Wybierz istniejące **\<resource-group-name>** na podstawie parametrów. |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wejść **\<virtual-network-name>**                                    |
    | Region           | Zaznaczenia **\<region-name>** |

3. Wybierz kartę **adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

4. Na karcie **adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresowa IPv4 | Wejść **\<IPv4-address-space>** |

5. W obszarze **Nazwa podsieci**wybierz pozycję **domyślny**wyraz.

6. W obszarze **Edytuj podsieć**wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wejść **\<subnet-name>** |
    | Zakres adresów podsieci | Wejść **\<subnet-address-range>**

7. Wybierz pozycję **Zapisz**.

8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

9. Wybierz przycisk **Utwórz**.
---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78244981"
---
## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > sieci > sieci wirtualnej** lub Wyszukaj **sieć wirtualną** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną**wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartościami**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **Utwórz nowy**, wprowadź ** \<nazwę zasobu-grupy->**, a następnie wybierz pozycję OK lub wybierz istniejącą ** \<>nazwy zasobu lub grupy** w oparciu o parametry. |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź ** \<nazwę sieci wirtualnej>**                                    |
    | Region           | Wybierz ** \<nazwę regionu i>** |

3. Wybierz kartę **adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

4. Na karcie **adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresów IPv4 | Wprowadź ** \<adres IPv4-przestrzeń>** |

5. W obszarze **Nazwa podsieci**wybierz pozycję **domyślny**wyraz.

6. W obszarze **Edytuj podsieć**wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wprowadź ** \<nazwę podsieci>** |
    | Zakres adresów podsieci | Wprowadź ** \<>zakresu adresów podsieci**

7. Wybierz pozycję **Zapisz**.

8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

9. Wybierz przycisk **Utwórz**.
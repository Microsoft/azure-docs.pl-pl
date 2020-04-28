---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeca3550b5fc58694779e7e54ce6ca547ba30e17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183846"
---
Każdy obiekt blob w usłudze Azure Storage musi znajdować się w kontenerze. Kontener jest częścią nazwy obiektu blob. Na przykład `mycontainer` to nazwa kontenera w następujących przykładowych identyfikatorach URI obiektów blob:

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

Nazwa kontenera musi być prawidłową nazwą DNS zgodną z następującymi zasadami nazewnictwa:

1. Nazwa kontenera musi zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-).
2. Bezpośrednio przed każdym znakiem kreski (-) i bezpośrednio po nim musi występować litera lub cyfra. W nazwach kontenerów następujące po sobie kreski są niedozwolone.
3. Wszystkie litery w nazwie kontenera muszą być małymi literami.
4. Nazwy kontenerów muszą zawierać od 3 do 63 znaków.

> [!IMPORTANT]
> Należy pamiętać, że nazwa kontenera może zawierać tylko małe litery. Jeśli w nazwie kontenera wystąpi wielka litera lub reguły nazewnictwa dotyczące kontenerów zostaną naruszone w inny sposób, może zostać wyświetlony błąd 400 (Nieprawidłowe żądanie). 
> 
> 


---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183275"
---
<!--v-sharos 10/13/2105 virtual device security-->

W przypadku korzystania z urządzenia wirtualnego StorSimple należy pamiętać o następujących kwestiach dotyczących zabezpieczeń:

* Urządzenie wirtualne jest zabezpieczone za pomocą subskrypcji Microsoft Azure. Oznacza to, że jeśli korzystasz z urządzenia wirtualnego, a subskrypcja platformy Azure zostanie naruszona, dane przechowywane na urządzeniu wirtualnym są również podatne.
* Klucz publiczny certyfikatu służący do szyfrowania danych przechowywanych w usłudze Azure StorSimple jest bezpiecznie dostępny dla klasycznego portalu Azure, a klucz prywatny jest przechowywany za pomocą urządzenia StorSimple. Na urządzeniu wirtualnym StorSimple klucze publiczny i prywatny są przechowywane na platformie Azure.
* Urządzenie wirtualne jest hostowane w centrum danych Microsoft Azure.


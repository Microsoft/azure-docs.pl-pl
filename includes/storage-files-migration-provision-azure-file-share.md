---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: c003e765574d764b6653823e8554d0718ea85e0e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081105"
---
Udział plików platformy Azure jest przechowywany w chmurze na koncie usługi Azure Storage.
Poniżej znajdują się inne zagadnienia dotyczące wydajności.

Jeśli masz wysoce aktywne udziały (udziały używane przez wielu użytkowników i/lub aplikacje), wówczas dwa udziały plików platformy Azure mogą osiągnąć limit wydajności konta magazynu.

Najlepszym rozwiązaniem jest wdrożenie kont magazynu z jednym udziałem plików.
Można umieścić wiele udziałów plików platformy Azure w ramach tego samego konta magazynu, na przykład w przypadku udziałów archiwalnych lub w przypadku niewielkich codziennych działań.

Te zagadnienia dotyczą tylko bezpośredniego dostępu do chmury (za pośrednictwem maszyny wirtualnej platformy Azure Azure File Sync). Jeśli planujesz używać Azure File Sync tylko w tych udziałach, to zgrupowanie kilku na jedno konto usługi Azure Storage jest odpowiednie.

Jeśli utworzono listę udziałów, należy zmapować każdy udział na konto magazynu, w którym będzie się znajdować.

W poprzedniej fazie określono odpowiednią liczbę udziałów. W tym kroku utworzono mapowanie kont magazynu do udziałów plików. Teraz Wdróż odpowiednią liczbę kont usługi Azure Storage z odpowiednią liczbą udziałów plików platformy Azure.

Upewnij się, że region każdego z kont magazynu jest taki sam i jest zgodny z regionem zasobu usługi synchronizacji magazynu, który został już wdrożony.

> [!CAUTION]
> Jeśli tworzysz udział plików platformy Azure z limitem 100 TiB, ten udział może korzystać tylko z opcji nadmiarowości magazynu lokalnie nadmiarowego lub strefy nadmiarowej. Przed użyciem udziałów plików 100 TiB należy wziąć pod uwagę potrzeby nadmiarowości magazynu.

Udziały plików platformy Azure są nadal tworzone domyślnie z limitem 5 TiB. Ponieważ tworzysz nowe konta magazynu, upewnij się, że postępuj zgodnie ze [wskazówkami dotyczącymi tworzenia kont magazynu, które zezwalają na udziały plików platformy Azure z limitami 100-TIB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Innym zagadnieniem podczas wdrażania konta magazynu jest nadmiarowość usługi Azure Storage. Zobacz [Opcje nadmiarowości usługi Azure Storage](../articles/storage/common/storage-redundancy.md).

Nazwy zasobów są również ważne. Na przykład Jeśli grupujesz wiele udziałów dla działu kadr na koncie usługi Azure Storage, musisz odpowiednio nazwać konto magazynu. Podobnie podczas nadawania nazw udziałom plików platformy Azure należy używać nazw podobnych do tych, które są używane na potrzeby swoich lokalnych odpowiedników.
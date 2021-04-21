---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c54bdc2738f9455926882c439ff8942c1c0a5a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759772"
---
Udziały plików platformy Azure są wdrażane na *kontach* magazynu , które są obiektami najwyższego poziomu, które reprezentują udostępnioną pulę magazynu. Ta pula magazynu może służyć do wdrażania wielu udziałów plików, a także innych zasobów magazynu, takich jak kontenery obiektów blob, kolejki lub tabele. Wszystkie zasoby magazynu wdrożone na koncie magazynu współdzielą limity, które mają zastosowanie do tego konta magazynu. Aby uzyskać bieżące limity konta magazynu, [zobacz Azure Files cele dotyczące skalowalności i wydajności.](../articles/storage/files/storage-files-scale-targets.md)

Istnieją dwa główne typy kont magazynu, które będą Azure Files wdrożeniach: 
- **Konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2):** konta magazynu GPv2 umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach standardowych/twardych (opartych na dyskach twardych). Oprócz przechowywania udziałów plików platformy Azure konta magazynu GPv2 mogą przechowywać inne zasoby magazynu, takie jak kontenery obiektów blob, kolejki lub tabele. 
- **Konta magazynu FileStorage:** konta magazynu FileStorage umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach SSD w warstwie Premium/SSD. Konta FileStorage mogą być używane tylko do przechowywania udziałów plików platformy Azure; Na koncie FileStorage nie można wdrożyć żadnych innych zasobów magazynu (kontenerów obiektów blob, kolejek, tabel itp.). Tylko konta FileStorage mogą wdrażać udziały plików SMB i NFS.

Istnieje kilka innych typów kont magazynu, które mogą wystąpić w interfejsie Azure Portal, powershell lub interfejsie wiersza polecenia. Dwa typy kont magazynu, BlockBlobStorage i BlobStorage, nie mogą zawierać udziałów plików platformy Azure. Dwa inne typy kont magazynu, które możesz zobaczyć, to konta ogólnego przeznaczenia w wersji 1 (GPv1) i klasyczne konta magazynu, które mogą zawierać udziały plików platformy Azure. Mimo że konta GPv1 i klasyczne konta magazynu mogą zawierać udziały plików platformy Azure, większość nowych funkcji usługi Azure Files jest dostępna tylko w przypadku kont magazynu GPv2 i FileStorage. W związku z tym zaleca się używanie kont magazynu GPv2 i FileStorage tylko w przypadku nowych wdrożeń oraz uaktualnianie kont GPv1 i klasycznych kont magazynu, jeśli już istnieją w środowisku.  
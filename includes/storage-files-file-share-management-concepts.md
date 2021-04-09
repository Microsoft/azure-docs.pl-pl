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
ms.openlocfilehash: 8d26b6c92462abc13c42257d2c6e571156eacc0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011760"
---
Udziały plików platformy Azure są wdrażane na *kontach magazynu*, które są obiektami najwyższego poziomu reprezentującymi udostępnioną pulę magazynu. Ta pula magazynu może służyć do wdrażania wielu udziałów plików, a także innych zasobów magazynu, takich jak kontenery obiektów blob, kolejki lub tabele. Wszystkie zasoby magazynu, które są wdrożone na koncie magazynu, współużytkują limity, które mają zastosowanie do tego konta magazynu. Aby wyświetlić bieżące limity dla konta magazynu, zobacz [Azure Files cele dotyczące skalowalności i wydajności](../articles/storage/files/storage-files-scale-targets.md).

Istnieją dwa główne typy kont magazynu, które będą używane na potrzeby wdrożeń Azure Files: 
- **Konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)**: GPv2 konta magazynu umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach standardowych/twardych. Oprócz przechowywania udziałów plików platformy Azure GPv2 konta magazynu mogą przechowywać inne zasoby magazynu, takie jak kontenery obiektów blob, kolejki lub tabele. 
- **FileStorage kont magazynu**: FileStorage konta magazynu umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach Premium/SSD (opartym na dysku półprzewodnikowym). Kont FileStorage można używać tylko do przechowywania udziałów plików platformy Azure. nie można wdrażać innych zasobów magazynu (kontenerów obiektów blob, kolejek, tabel itp.) w ramach konta FileStorage. Tylko konta FileStorage mogą wdrażać udziały plików SMB i NFS.

Istnieje kilka innych typów kont magazynu, które mogą znajdować się w Azure Portal, PowerShell lub interfejsie wiersza polecenia. Dwa typy kont magazynu, konta magazynu BlockBlobStorage i BlobStorage nie mogą zawierać udziałów plików platformy Azure. Pozostałe dwa typy kont magazynu, które mogą być widoczne, to GPv1 (ogólnego przeznaczenia) i klasyczne konta magazynu, z których oba mogą zawierać udziały plików platformy Azure. Chociaż GPv1 i klasyczne konta magazynu mogą zawierać udziały plików platformy Azure, większość nowych funkcji Azure Files są dostępne tylko na kontach magazynu GPv2 i FileStorage. Dlatego zalecamy używanie kont magazynu GPv2 i FileStorage w przypadku nowych wdrożeń oraz uaktualnianie GPv1 i klasycznych kont magazynu, jeśli już istnieją w danym środowisku.  
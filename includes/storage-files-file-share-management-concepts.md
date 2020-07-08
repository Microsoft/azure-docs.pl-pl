---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597890"
---
Udziały plików platformy Azure są wdrażane na *kontach magazynu*, które są obiektami najwyższego poziomu reprezentującymi udostępnioną pulę magazynu. Ta pula magazynu może służyć do wdrażania wielu udziałów plików, a także innych zasobów magazynu, takich jak kontenery obiektów blob, kolejki lub tabele. Wszystkie zasoby magazynu, które są wdrożone na koncie magazynu, współużytkują limity, które mają zastosowanie do tego konta magazynu. Aby wyświetlić bieżące limity dla konta magazynu, zobacz [Azure Files cele dotyczące skalowalności i wydajności](../articles/storage/files/storage-files-scale-targets.md).

Istnieją dwa główne typy kont magazynu, które będą używane na potrzeby wdrożeń Azure Files: 
- **Konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)**: GPv2 konta magazynu umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach standardowych/twardych. Oprócz przechowywania udziałów plików platformy Azure GPv2 konta magazynu mogą przechowywać inne zasoby magazynu, takie jak kontenery obiektów blob, kolejki lub tabele. 
- **FileStorage kont magazynu**: FileStorage konta magazynu umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach Premium/SSD (opartym na dysku półprzewodnikowym). Kont FileStorage można używać tylko do przechowywania udziałów plików platformy Azure. nie można wdrażać innych zasobów magazynu (kontenerów obiektów blob, kolejek, tabel itp.) w ramach konta FileStorage.

Istnieje kilka innych typów kont magazynu, które mogą znajdować się w Azure Portal, PowerShell lub interfejsie wiersza polecenia. Dwa typy kont magazynu, konta magazynu BlockBlobStorage i BlobStorage nie mogą zawierać udziałów plików platformy Azure. Pozostałe dwa typy kont magazynu, które mogą być widoczne, to GPv1 (ogólnego przeznaczenia) i klasyczne konta magazynu, z których oba mogą zawierać udziały plików platformy Azure. Chociaż GPv1 i klasyczne konta magazynu mogą zawierać udziały plików platformy Azure, większość nowych funkcji Azure Files są dostępne tylko na kontach magazynu GPv2 i FileStorage. Dlatego zalecamy używanie kont magazynu GPv2 i FileStorage w przypadku nowych wdrożeń oraz uaktualnianie GPv1 i klasycznych kont magazynu, jeśli już istnieją w danym środowisku.  
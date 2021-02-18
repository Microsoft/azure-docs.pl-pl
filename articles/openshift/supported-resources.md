---
title: Obsługiwane zasoby dla usługi Azure Red Hat OpenShift 3.11
description: Dowiedz się, które regiony platformy Azure i rozmiary maszyn wirtualnych są obsługiwane przez Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ad0837ae110b84cdff690fe76e13923a0ab60996
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635612"
---
# <a name="azure-red-hat-openshift-resources"></a>Zasoby usługi Azure Red Hat OpenShift

Ten temat zawiera listę regionów platformy Azure i rozmiary maszyn wirtualnych obsługiwane przez Microsoft Azure usługi Red Hat OpenShift 3,11.

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Zobacz temat [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) , aby zapoznać się z bieżącą listą regionów, w których można wdrożyć klastry usługi Azure Red Hat OpenShift.

## <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych

Poniżej przedstawiono obsługiwane rozmiary maszyn wirtualnych, które można określić dla węzłów obliczeniowych w klastrze Red Hat OpenShift platformy Azure.

> [!Important]
> Każda maszyna wirtualna ma inną liczbę dysków, które można dołączać. Może to nie być tak samo jasne jak rozmiar pamięci lub procesora CPU.
> Nie wszystkie rozmiary maszyn wirtualnych są dostępne we wszystkich regionach. Nawet jeśli interfejs API obsługuje określony rozmiar, może wystąpić błąd, jeśli rozmiar nie jest dostępny w określonym regionie.
> Aby uzyskać więcej informacji [, zobacz bieżącą listę obsługiwanych rozmiarów maszyn wirtualnych na region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .

## <a name="compute-node-sizes"></a>Rozmiary węzłów obliczeniowych

Następujące rozmiary węzłów obliczeniowych są obsługiwane przez interfejs API REST usługi Azure Red Hat OpenShift:

|Rozmiar|Procesor wirtualny|Pamięć RAM|
|-|-|-|
|Standardowa D4S v3|4|16 GB|
|Standardowa D8s v3|8|32 GB|
|Standardowa D16s v3|16|64 GB|
|Standardowa D32s v3|32|128 GB|
|-|-|-|
|Standardowa E4s v3|4|32 GB|
|Standardowa E8s v3|8|64 GB|
|Standardowa E16s v3|16|128 GB|
|Standardowa E32s v3|32|256 GB|
|-|-|-|
|Standardowa F8s v2|8|16 GB|
|Standardowa F16s v2|16|32 GB|
|Standardowa F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Rozmiary węzłów głównych

Poniższe rozmiary węzła głównego/infrastruktury są obsługiwane przez interfejs API REST usługi Azure Red Hat OpenShift:

|Rozmiar|Procesor wirtualny|Pamięć RAM|
|-|-|-|
|Standardowa D4S v3|4|16 GB|
|Standardowa D8s v3|8|32 GB|
|Standardowa D16s v3|16|64 GB|
|Standardowa D32s v3|32|128 GB|

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) .

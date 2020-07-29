---
title: 'Odwołanie: zaniechanie obrazu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Szczegóły dotyczące zaniechania wpływu na Data Science Virtual Machine platformy Azure
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80754772"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Odwołanie: zaniechanie obrazów DSVM

Poniżej omówiono sugestie dotyczące sposobu postępowania z nadchodzącymi zastosowaniami w usłudze Azure Data Science Virtual Machine.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Migrowanie dysków danych

Nie będziemy obsługiwać obrazu DSVM systemu Windows 2012 w dniu 31 grudnia 2019. Aby przeprowadzić migrację dysku danych z istniejącego systemu Windows 2012 DSVM do systemu Windows 2016 DSVM, wykonaj następujące czynności:

1. Utwórz nowy system Windows 2016 DSVM, postępując zgodnie z instrukcjami przedstawionymi [tutaj](./provision-vm.md#create-your-dsvm).
1. Odłącz istniejące dyski danych z obrazu systemu Windows 2012, korzystając z [tych instrukcji](../../virtual-machines/windows/detach-disk.md).
1. Dołącz dysk z poprzedniego kroku do obrazu systemu Windows 2016, korzystając z [tych instrukcji](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Nowi użytkownicy powinni używać najnowszych obrazów Ubuntu lub Windows. CentOS pozostanie dostępne do użycia z istniejącymi szablonami rozwiązań.
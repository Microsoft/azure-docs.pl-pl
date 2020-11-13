---
title: Zaświadczanie enclaves na platformie Azure
description: Dowiedz się, jak można użyć zaświadczania, aby sprawdzić, czy poufne środowisko obliczeniowe jest bezpieczne
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: 86856d1f66b5b7d723c907c17d7179ffcd2b82ab
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565674"
---
# <a name="attesting-sgx-enclaves"></a>Zaświadczanie SGX Enclaves

Dane poufne na platformie Azure oferują maszyny wirtualne oparte na technologii Intel SGX, które mogą izolować część kodu lub danych. Podczas pracy z tymi [enclavesami](confidential-computing-enclaves.md)należy uzyskać weryfikację i weryfikację, że zaufane środowisko jest bezpieczne. Ta weryfikacja jest procesem zaświadczania. 

## <a name="overview"></a>Omówienie 

Zaświadczanie umożliwia jednostce uzależnionej zwiększenie pewności, że ich oprogramowanie (1) działa w enklawy i (2), że enklawy jest aktualne i bezpieczne. Na przykład enklawy prosi podstawowy sprzęt do wygenerowania poświadczeń, które obejmują potwierdzenie, że enklawy istnieje na platformie. Raport może następnie zostać przekazany do drugiego enklawy, który sprawdza, czy raport został wygenerowany na tej samej platformie.

![zaświadcz kod w enklawy](media/attestation/attestation.png)



Zaświadczanie należy zaimplementować przy użyciu bezpiecznej usługi zaświadczania zgodnej z oprogramowaniem systemowym i krzemem. Przykłady usług, których można użyć, to

- [Zaświadczanie Microsoft Azure (wersja zapoznawcza)](../attestation/overview.md) lub
- [Usługi zaświadczania i aprowizacji firmy Intel](https://software.intel.com/sgx/attestation-services)


są one zgodne z poufną infrastrukturą Azure SGX. 

## <a name="next-steps"></a>Następne kroki
Wypróbuj [przykłady Zaświadczania Microsoft Azure dla aplikacji obsługujących enklawy](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/).
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
ms.openlocfilehash: 70a17aacde67744eae74ca263200f2c65fbd300a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997830"
---
# <a name="attesting-sgx-enclaves"></a>Zaświadczanie SGX Enclaves

Dane poufne na platformie Azure oferują maszyny wirtualne oparte na technologii Intel SGX, które mogą izolować część kodu lub danych. Podczas pracy z tymi [enclavesami](confidential-computing-enclaves.md)należy uzyskać weryfikację i weryfikację, że zaufane środowisko jest bezpieczne. Ta weryfikacja jest procesem zaświadczania. 

## <a name="overview"></a>Omówienie 

Zaświadczanie umożliwia jednostce uzależnionej zwiększenie pewności, że ich oprogramowanie (1) działa w enklawy i (2), że enklawy jest aktualne i bezpieczne. Na przykład enklawy prosi podstawowy sprzęt do wygenerowania poświadczeń, które obejmują potwierdzenie, że enklawy istnieje na platformie. Raport może następnie zostać przekazany do drugiego enklawy, który sprawdza, czy raport został wygenerowany na tej samej platformie.

![zaświadcz kod w enklawy](media/attestation/attestation.png)



Zaświadczanie należy zaimplementować przy użyciu bezpiecznej usługi zaświadczania zgodnej z oprogramowaniem systemowym i krzemem. Przykłady usług, których można użyć, to

- [Zaświadczanie Microsoft Azure (wersja zapoznawcza)](https://docs.microsoft.com/azure/attestation/overview) lub
- [Usługi zaświadczania i aprowizacji firmy Intel](https://software.intel.com/sgx/attestation-services)


są one zgodne z poufną infrastrukturą Azure SGX. 

## <a name="next-steps"></a>Następne kroki
Wypróbuj [przykłady Zaświadczania Microsoft Azure dla aplikacji obsługujących enklawy](https://docs.microsoft.com/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/).
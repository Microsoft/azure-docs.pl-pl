---
title: Konfigurowanie prywatnego punktu końcowego (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Użyj prywatnego linku platformy Azure, aby bezpiecznie uzyskać dostęp do obszaru roboczego Azure Machine Learning z sieci wirtualnej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/03/2020
ms.openlocfilehash: b9b256a3d3c1636cac55bcb1790182240d2199c0
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661876"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Konfigurowanie prywatnego linku platformy Azure dla obszaru roboczego Azure Machine Learning (wersja zapoznawcza)

W tym dokumencie dowiesz się, jak korzystać z prywatnego linku platformy Azure z obszarem roboczym Azure Machine Learning. Aby uzyskać informacje na temat konfigurowania sieci wirtualnej dla Azure Machine Learning, zobacz [Omówienie izolacji i prywatności w sieci wirtualnej](how-to-network-security-overview.md)

> [!IMPORTANT]
> Korzystanie z prywatnego linku platformy Azure z obszarem roboczym Azure Machine Learning jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna tylko w regionach **Wschodnie stany USA**, **Stany** USA i **zachodnie** stany USA 2. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Link prywatny platformy Azure umożliwia nawiązanie połączenia z obszarem roboczym przy użyciu prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w sieci wirtualnej. Następnie można ograniczyć dostęp do obszaru roboczego tylko w przypadku prywatnych adresów IP. Link prywatny pomaga ograniczyć ryzyko związane z eksfiltracji danych. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz artykuł [prywatny link do platformy Azure](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Łącze prywatne platformy Azure nie wpływa na płaszczyznę kontroli platformy Azure (operacje zarządzania), takie jak usuwanie obszaru roboczego lub zarządzanie zasobami obliczeniowymi. Na przykład tworzenie, aktualizowanie lub usuwanie elementu docelowego obliczeń. Te operacje są wykonywane za pośrednictwem publicznej sieci Internet jako normalne. Operacje płaszczyzny danych, takie jak używanie Azure Machine Learning Studio, interfejsów API (w tym opublikowanych potoków) lub zestawu SDK korzystają z prywatnego punktu końcowego.
>
> W przypadku korzystania z przeglądarki Mozilla Firefox mogą wystąpić problemy podczas próby uzyskania dostępu do prywatnego punktu końcowego dla obszaru roboczego. Ten problem może dotyczyć systemu DNS za pośrednictwem protokołu HTTPS w Mozilla. Zalecamy korzystanie z przeglądarki Google Chrome firmy Microsoft jako obejście problemu.

> [!TIP]
> Wystąpienie obliczeniowe Azure Machine Learning może być używane z obszarem roboczym i prywatnym punktem końcowym. Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej w regionach **Wschodnie stany USA**, **Południowo-środkowe** stany USA i **zachodnie stany USA 2** .

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Tworzenie obszaru roboczego korzystającego z prywatnego punktu końcowego

> [!IMPORTANT]
> Obecnie obsługujemy tylko Włączanie prywatnego punktu końcowego podczas tworzenia nowego obszaru roboczego Azure Machine Learning.

Szablon w programie [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) może służyć do tworzenia obszaru roboczego z prywatnym punktem końcowym.

Aby uzyskać informacje na temat korzystania z tego szablonu, w tym prywatnych punktów końcowych, zobacz [Tworzenie obszaru roboczego dla Azure Machine Learning za pomocą szablonu Azure Resource Manager](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Używanie obszaru roboczego w prywatnym punkcie końcowym

Ponieważ komunikacja z obszarem roboczym jest dozwolona tylko w sieci wirtualnej, wszystkie środowiska deweloperskie korzystające z obszaru roboczego muszą należeć do sieci wirtualnej. Na przykład maszyna wirtualna w sieci wirtualnej.

> [!IMPORTANT]
> Aby uniknąć tymczasowego zakłócenia łączności, firma Microsoft zaleca opróżnianie pamięci podręcznej DNS na komputerach łączących się z obszarem roboczym po włączeniu linku prywatnego. 

Aby uzyskać informacje na temat usługi Azure Virtual Machines, zobacz [dokumentację Virtual Machines](/azure/virtual-machines/).


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zabezpieczania obszaru roboczego Azure Machine Learning, zobacz artykuł [Omówienie izolacji i prywatności w sieci wirtualnej](how-to-network-security-overview.md) .
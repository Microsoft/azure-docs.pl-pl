---
title: Azure Key Vault światy zabezpieczeń | Microsoft Docs
description: Azure Key Vault to usługa z wieloma dzierżawami. Używa ona puli sprzętowych modułów zabezpieczeń w każdej lokalizacji platformy Azure. Wszystkie lokalizacje w regionie geograficznym współdzielą granicę kryptograficzną.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: d21deea13aac3d40c452a183c340d3108a1a01f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936332"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault światy zabezpieczeń i granice geograficzne

Azure Key Vault jest usługą z wieloma dzierżawami i używa puli sprzętowych modułów zabezpieczeń (sprzętowych modułów zabezpieczeń) w każdej lokalizacji platformy Azure. 

Wszystkie sprzętowych modułów zabezpieczeń w lokalizacjach platformy Azure w tym samym regionie geograficznym mają tę samą granicę kryptograficzną (firmy Thales Security World). Na przykład Wschodnie stany USA i zachodnie stany USA współdzielą ten sam świecie zabezpieczeń, ponieważ należą do lokalizacji geograficznej USA. Podobnie wszystkie lokalizacje platformy Azure w Japonii współdzielą ten sam świat zabezpieczeń i wszystkie lokalizacje platformy Azure w Australii, Indiach i tak dalej. 

## <a name="backup-and-restore-behavior"></a>Zachowanie tworzenia kopii zapasowych i przywracania

Kopię zapasową klucza z magazynu kluczy w jednej lokalizacji platformy Azure można przywrócić do magazynu kluczy w innej lokalizacji platformy Azure, o ile są spełnione oba te warunki:

- Obie lokalizacje platformy Azure należą do tej samej lokalizacji geograficznej
- Oba magazyny kluczy należą do tej samej subskrypcji platformy Azure

Przykładowo kopia zapasowa wykonywana przez daną subskrypcję klucza w magazynie kluczy w regionie zachodnie Indie może zostać przywrócona tylko do innego magazynu kluczy w tej samej subskrypcji i lokalizacji geograficznej; Indie Zachodnie, Indie Środkowe lub Indie Południowe.

## <a name="regions-and-products"></a>Regiony i produkty

- [Regiony platformy Azure](https://azure.microsoft.com/regions/)
- [Produkty firmy Microsoft według regionów](https://azure.microsoft.com/regions/services/)

Regiony są mapowane na światowe środowiska zabezpieczeń, pokazane jako główne nagłówki w tabelach:

W artykule produkty według regionów na przykład na karcie **Ameryki Północnej** znajdują się Wschodnie stany USA, środkowe stany USA, zachodnie Stany US — wszystkie mapowanie do regionu Ameryka Północna. 

>[!NOTE]
>Wyjątkiem jest to, że Stany USA i US DOD USA mają własne światowe zabezpieczenia. 

Podobnie na karcie **Europa** , Europa Północna i Europa Zachodnia są mapowane do regionu Europa. Ta sama wartość dotyczy również karty **Azja i Pacyfik** .

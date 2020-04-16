---
title: Światy zabezpieczeń usługi Azure Key Vault | Dokumenty firmy Microsoft
description: Usługa Azure Key Vault to usługa dla wielu dzierżawców. Używa puli modułów HSM w każdej lokalizacji platformy Azure. Wszystkie lokalizacje w regionie geograficznym współużytkują granicę kryptograficzną.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: 3584f83c5e1a5e83d069373395227b70c084eae9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428955"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Światy zabezpieczeń i granice geograficzne usługi Azure Key Vault

Usługa Azure Key Vault jest usługą wielodostępną i używa puli sprzętowych modułów zabezpieczeń (HSM) w każdej lokalizacji platformy Azure. 

Wszystkie moduły HSM w lokalizacjach platformy Azure w tym samym regionie geograficznym mają tę samą granicę kryptograficzną (Thales Security World). Na przykład wschodnie stany USA i zachodnie stany USA mają ten sam świat zabezpieczeń, ponieważ należą do lokalizacji geograficznej w USA. Podobnie wszystkie lokalizacje platformy Azure w Japonii mają ten sam świat zabezpieczeń i wszystkie lokalizacje platformy Azure w Australii, Indiach i tak dalej. 

## <a name="backup-and-restore-behavior"></a>Zachowanie tworzenia kopii zapasowych i przywracania

Kopię zapasową pobraną z klucza z magazynu kluczy w jednej lokalizacji platformy Azure można przywrócić do magazynu kluczy w innej lokalizacji platformy Azure, o ile oba te warunki są spełnione:

- Obie lokalizacje platformy Azure należą do tej samej lokalizacji geograficznej
- Oba magazyny kluczy należą do tej samej subskrypcji platformy Azure

Na przykład kopia zapasowa pobrana przez daną subskrypcję klucza w magazynie kluczy w Indiach Zachodnich może zostać przywrócona tylko do innego magazynu kluczy w tej samej lokalizacji subskrypcji i lokalizacji geograficznej; Indie Zachodnie, Indie Środkowe czy Południowe Indie.

## <a name="regions-and-products"></a>Regiony i produkty

- [Regiony platformy Azure](https://azure.microsoft.com/regions/)
- [Produkty firmy Microsoft według regionów](https://azure.microsoft.com/regions/services/)

Regiony są mapowane na światy zabezpieczeń, wyświetlane jako główne nagłówki w tabelach:

W artykule Produkty według regionów, na przykład karta Ameryka zawiera wschodnie stany USA, **środkowe** stany USA, zachodnie stany USA wszystkie mapowanie do regionu Obu Ameryk. 

>[!NOTE]
>Wyjątkiem jest to, że US DOD EAST i US DOD CENTRAL mają własne światy bezpieczeństwa. 

Podobnie, na karcie **Europa Północna** i EUROPA ZACHODNIA, obie mapują region Europy. To samo dotyczy również karty **Azja i Pacyfik.**




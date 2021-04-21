---
title: Azure Key Vault security worlds | Microsoft Docs
description: Azure Key Vault jest usługą wielodostępną. Używa puli modułów HSM w każdym regionie świadczenia usługi Azure. Wszystkie regiony w regionie geograficznym mają granice kryptograficzne.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0d82a3cb4c08d47b6827072378b9827037d32412
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751818"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault zabezpieczeń i granice geograficzne

Produkty platformy Azure są dostępne w wielu lokalizacjach [geograficznych platformy Azure,](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)a każda lokalizacja geograficzna platformy Azure zawiera co najmniej jeden region. Na przykład lokalizacja geograficzna Europa zawiera dwa regiony — Europa Północna i Europa Zachodnia — a jedynym regionem w geografii Brazylii jest Brazylia Południowa.

Azure Key Vault to usługa wielodostępna, która używa puli sprzętowych modułów zabezpieczeń (HSM). Wszystkie moduły HSM w lokalizacji geograficznej mają tę samą granicę kryptograficznych, nazywaną "światem zabezpieczeń". Każda lokalizacja geograficzna odpowiada pojedynczemu światowi zabezpieczeń i na odwrót.

Wschodnie stany USA i Zachodnie stany USA mają ten sam świat zabezpieczeń, ponieważ należą do lokalizacji geograficznej (Stany Zjednoczone). Podobnie wszystkie regiony platformy Azure w Japonii mają ten sam świat zabezpieczeń, co wszystkie regiony platformy Azure w Australii itd.

>[!NOTE]
> Wyjątkiem jest to, że us dod EAST i US DOD CENTRAL mają własne światy zabezpieczeń.

## <a name="backup-and-restore-behavior"></a>Zachowanie tworzenia kopii zapasowej i przywracania

Kopię zapasową klucza z magazynu kluczy w jednym regionie świadczenia usługi Azure można przywrócić do magazynu kluczy w innym regionie świadczenia usługi Azure, o ile spełnione są oba te warunki:

- Oba regiony platformy Azure należą do tej samej lokalizacji geograficznej.
- Oba magazyny kluczy należą do tej samej subskrypcji platformy Azure.

Na przykład kopię zapasową klucza w magazynie kluczy Indie Zachodnie można przywrócić do innego magazynu kluczy w tej samej subskrypcji w lokalizacji geograficznej Indie Zachodnie, Indie Środkowe i Indie Południowe.

## <a name="next-steps"></a>Następne kroki

- Zobacz [produkty firmy Microsoft według regionów](https://azure.microsoft.com/regions/services/)

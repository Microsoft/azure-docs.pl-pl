---
title: Azure Key Vault magazynu do innej grupy zasobów | Microsoft Docs
description: Wskazówki dotyczące przenoszenia magazynu kluczy do innej grupy zasobów.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 14ecbcaa35153438601a3dabb70f5b38006ded1b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749568"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Przenoszenie magazynu kluczy platformy Azure między grupami zasobów

## <a name="overview"></a>Omówienie

Przenoszenie magazynu kluczy między grupami zasobów jest obsługiwaną funkcją magazynu kluczy. Przenoszenie magazynu kluczy między grupami zasobów nie będzie miało wpływu na konfigurację zapory magazynu kluczy ani zasad dostępu. Połączone aplikacje i jednostki usługi powinny nadal działać zgodnie z zamierzeniami.

> [!IMPORTANT]
> **Magazynów kluczy używanych do szyfrowania dysków nie można przenosić.**
> Jeśli używasz magazynu kluczy z szyfrowaniem dysków dla maszyny wirtualnej, magazynu kluczy nie można przenieść do innej grupy zasobów ani subskrypcji, gdy jest włączone szyfrowanie dysków. Przed przeniesieniem magazynu kluczy do nowej grupy zasobów lub subskrypcji należy wyłączyć szyfrowanie dysków. 

## <a name="design-considerations"></a>Zagadnienia projektowe

Organizacja może wdrożyć zasady Azure Policy wymuszania lub wykluczeń na poziomie grupy zasobów. W grupie zasobów, w której obecnie istnieje magazyn kluczy, i grupie zasobów, do której jest przenoszący magazyn kluczy, może być inny zestaw przypisań zasad. Konflikt wymagań dotyczących zasad może potencjalnie przerwać aplikacje.

### <a name="example"></a>Przykład

Masz aplikację połączioną z magazynem kluczy, która tworzy certyfikaty ważne przez dwa lata. Grupa zasobów, do której próbujesz przenieść magazyn kluczy, ma przypisanie zasad, które blokuje tworzenie certyfikatów, które są ważne dłużej niż jeden rok. Po przeniesieniu magazynu kluczy do nowej grupy zasobów operacja utworzenia certyfikatu ważnego przez dwa lata zostanie zablokowana przez przypisanie zasad platformy Azure.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że przejdź do strony Azure Policy na stronie Azure Portal i przyjrzyj się przypisaniom zasad dla bieżącej grupy zasobów, a także grupie zasobów, do których przenosisz się, i upewnij się, że nie ma żadnych niezgodności.

## <a name="procedure"></a>Procedura

1. Logowanie do witryny Azure Portal
2. Przechodzenie do magazynu kluczy
3. Kliknij kartę "Przegląd"
4. Wybierz przycisk "Przenieś"
5. Wybierz pozycję "Przenieś do innej grupy zasobów" z opcji listy rozwijanej
6. Wybierz grupę zasobów, do której chcesz przenieść magazyn kluczy
7. Potwierdzenie ostrzeżenia dotyczące przenoszenia zasobów
8. Wybierz pozycję „OK”

Key Vault teraz oceni ważność przenoszenia zasobów i powiadomi o wszelkich błędach. Jeśli nie zostaną znalezione żadne błędy, przenoszenie zasobów zostanie ukończone. 

---
title: Azure Key Vault przenieść magazyn do innej grupy zasobów | Microsoft Docs
description: Wskazówki dotyczące przeniesienia magazynu kluczy do innej grupy zasobów.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: ea152e1d78ab1ea610eee5420394c89524673993
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92042386"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Przenoszenie magazynu kluczy platformy Azure między grupami zasobów

## <a name="overview"></a>Omówienie

Przeniesienie magazynu kluczy między grupami zasobów jest obsługiwaną funkcją magazynu kluczy. Przeniesienie magazynu kluczy między grupami zasobów nie wpłynie na zaporę magazynu kluczy ani konfiguracje zasad dostępu. Połączone aplikacje i jednostki usługi powinny nadal działały zgodnie z oczekiwaniami.

> [!IMPORTANT]
> **Magazyny kluczy używane do szyfrowania dysków nie mogą zostać przeniesione.**
> Jeśli używasz magazynu kluczy z szyfrowaniem dysków dla maszyny wirtualnej, nie można przenieść magazynu kluczy do innej grupy zasobów lub subskrypcji, gdy jest włączone szyfrowanie dysku. Przed przeniesieniem magazynu kluczy do nowej grupy zasobów lub subskrypcji należy wyłączyć szyfrowanie dysku. 

## <a name="design-considerations"></a>Zagadnienia projektowe

Twoja organizacja może zaimplementować Azure Policy z wymuszeniem lub wykluczeniam na poziomie grupy zasobów. W grupie zasobów, w której już istnieje Magazyn kluczy, może istnieć inny zestaw przypisań zasad i Grupa zasobów, w której jest przenoszony Magazyn kluczy. W przypadku konfliktu w wymaganiach dotyczących zasad istnieje możliwość przerwania aplikacji.

### <a name="example"></a>Przykład

Aplikacja jest połączona z magazynem kluczy, który tworzy certyfikaty, które są ważne przez dwa lata. Grupa zasobów, w której próbujesz przenieść magazyn kluczy, ma przypisanie zasad, które blokuje tworzenie certyfikatów, które są ważne przez okres dłuższy niż jeden rok. Po przeniesieniu magazynu kluczy do nowej grupy zasobów operacja utworzenia certyfikatu ważnego przez dwa lata zostanie zablokowana przez przypisanie zasady platformy Azure.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że przejdź do strony Azure Policy na Azure Portal i sprawdź przypisania zasad dla bieżącej grupy zasobów, a także grupę zasobów, do której chcesz przenieść, i upewnij się, że nie ma żadnych niezgodności.

## <a name="procedure"></a>Procedura

1. Logowanie do witryny Azure Portal
2. Przejdź do magazynu kluczy
3. Kliknij kartę "przegląd"
4. Wybierz przycisk "Przenieś"
5. Wybierz pozycję "Przenieś do innej grupy zasobów" z opcji listy rozwijanej
6. Wybierz grupę zasobów, w której chcesz przenieść magazyn kluczy
7. Potwierdzenie ostrzeżenia dotyczącego przeniesienia zasobów
8. Wybierz pozycję „OK”

Key Vault teraz oceni ważność przenoszonego zasobu i generuje alert o wszelkich błędach. Jeśli nie zostaną znalezione żadne błędy, przeniesienie zasobu zostanie ukończone. 

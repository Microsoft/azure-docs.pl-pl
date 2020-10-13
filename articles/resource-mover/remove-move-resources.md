---
title: Usuwanie zasobów z kolekcji przenoszenia w obszarze przenoszenia zasobów platformy Azure
description: Dowiedz się, jak usunąć zasoby z kolekcji Move w obszarze przenoszenia zasobów platformy Azure.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 241ccbda67f7a2518d0c44a0d362673922ad4284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89653129"
---
# <a name="remove-resources-from-a-move-collection"></a>Usuwanie zasobów z kolekcji przenoszenia

W tym artykule opisano sposób usuwania zasobów z kolekcji Move w [usłudze Azure Resource Recovery](overview.md). Kolekcje przenoszenia są używane podczas przenoszenia zasobów platformy Azure między regionami platformy Azure.

## <a name="remove-a-resource-portal"></a>Usuwanie zasobu (Portal)

Usuń w portalu przenoszenia zasobów w następujący sposób:

1. W **różnych regionach**Wybierz zasoby, które chcesz usunąć z kolekcji > **usunąć**.

    ![Przycisk do wybrania do usunięcia](./media/remove-move-resources/portal-select-resources.png)

1. W obszarze **usuwanie zasobów**kliknij pozycję **Usuń**.

    ![Przycisk służący do usuwania zasobów z kolekcji przenoszenia](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-resource-powershell"></a>Usuwanie zasobu (PowerShell)

Usuń zasób (w naszym przykładzie maszyn PSDemoVM) z kolekcji przy użyciu programu PowerShell w następujący sposób:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus - Name PSDemoVM
```
**Oczekiwane dane wyjściowe** 
 ![ Tekst wyjściowy po usunięciu zasobu z kolekcji przenoszenia](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>Usuwanie kolekcji (program PowerShell)

Usuń całą kolekcję przenoszenia przy użyciu programu PowerShell w następujący sposób:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus 
```
**Oczekiwane dane wyjściowe** 
 ![ Tekst wyjściowy po usunięciu kolekcji przenoszenia](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Stan zasobu maszyny wirtualnej po usunięciu

Co się stanie po usunięciu zasobu maszyny wirtualnej z kolekcji przenoszenia zależy od stanu zasobu, zgodnie z podsumowaniem w tabeli.

###  <a name="remove-vm-state"></a>Usuń stan maszyny wirtualnej
**Stan zasobu** | **MASZYN** | **Sieć**
--- | --- | --- 
**Dodano do kolekcji przenoszenia** | Usuń z kolekcji przenoszenia. | Usuń z kolekcji przenoszenia. 
**Zależności rozwiązane/przygotowywanie oczekujące** | Usuń z kolekcji przenoszenia  | Usuń z kolekcji przenoszenia. 
**Przygotowywanie w toku**<br/> (lub wszystkie inne stany w toku) | Operacja usuwania kończy się niepowodzeniem z powodu błędu.  | Operacja usuwania kończy się niepowodzeniem z powodu błędu.
**Przygotowywanie nie powiodło się** | Usuń z kolekcji przenoszenia.<br/>Usuń wszystkie elementy utworzone w regionie docelowym, w tym dyski replik. <br/><br/> Zasoby infrastruktury utworzone podczas przenoszenia muszą zostać usunięte ręcznie. | Usuń z kolekcji przenoszenia.  
**Inicjuj oczekiwanie na przeniesienie** | Usuń z kolekcji przenoszenia.<br/><br/> Usuń wszystkie elementy utworzone w regionie docelowym, w tym maszyny wirtualne, dyski repliki itp.  <br/><br/> Zasoby infrastruktury utworzone podczas przenoszenia muszą zostać usunięte ręcznie. | Usuń z kolekcji przenoszenia.
**Inicjowanie przenoszenia nie powiodło się** | Usuń z kolekcji przenoszenia.<br/><br/> Usuń wszystkie elementy utworzone w regionie docelowym, w tym maszyny wirtualne, dyski repliki itp.  <br/><br/> Zasoby infrastruktury utworzone podczas przenoszenia muszą zostać usunięte ręcznie. | Usuń z kolekcji przenoszenia.
**Oczekiwanie na zatwierdzenie** | Zalecamy odrzucenie przeniesienia, aby najpierw usunąć zasoby docelowe.<br/><br/> Zasób przechodzi z powrotem do stanu **inicjowania przenoszenia oczekującego** i można w dalszym ciągu kontynuować. | Zalecamy odrzucenie przeniesienia, aby najpierw usunąć zasoby docelowe.<br/><br/> Zasób przechodzi z powrotem do stanu **inicjowania przenoszenia oczekującego** i można w dalszym ciągu kontynuować. 
**Zatwierdzenie nie powiodło się** | Zalecamy odrzucanie, aby najpierw usunąć zasoby docelowe.<br/><br/> Zasób przechodzi z powrotem do stanu **inicjowania przenoszenia oczekującego** i można w dalszym ciągu kontynuować. | Zalecamy odrzucenie przeniesienia, aby najpierw usunąć zasoby docelowe.<br/><br/> Zasób przechodzi z powrotem do stanu **inicjowania przenoszenia oczekującego** i można w dalszym ciągu kontynuować.
**Odrzuć ukończone** | Zasób przechodzi z powrotem do stanu **inicjowania przenoszenia** .<br/><br/> Jest ona usuwana z kolekcji przenoszenia wraz z wszelkimi elementami utworzonymi na maszynie docelowej, dyskach repliki, magazynie itp.  <br/><br/> Zasoby infrastruktury utworzone podczas przenoszenia muszą zostać usunięte ręcznie. <br/><br/> Zasoby infrastruktury utworzone podczas przenoszenia muszą zostać usunięte ręcznie. |  Zasób przechodzi z powrotem do stanu **inicjowania przenoszenia** .<br/><br/> Jest ona usuwana z kolekcji przenoszenia.
**Nie można odrzucić** | Zalecamy odrzucanie ruchów w taki sposób, aby zasoby docelowe zostały usunięte jako pierwsze.<br/><br/> Następnie zasób powróci do stanu **inicjowania przenoszenia oczekujące** i możesz kontynuować od tego momentu. | Zalecamy odrzucanie ruchów w taki sposób, aby zasoby docelowe zostały usunięte jako pierwsze.<br/><br/> Następnie zasób powróci do stanu **inicjowania przenoszenia oczekujące** i możesz kontynuować od tego momentu.
**Oczekiwanie na usunięcie źródła** | Usunięte z kolekcji przenoszenia.<br/><br/> Nie usuwa żadnych elementów utworzonych w regionie docelowym.  | Usunięte z kolekcji przenoszenia.<br/><br/> Nie usuwa żadnych elementów utworzonych w regionie docelowym.
**Usuwanie źródła nie powiodło się** | Usunięte z kolekcji przenoszenia.<br/><br/> Nie usuwa żadnych elementów utworzonych w regionie docelowym. | Usunięte z kolekcji przenoszenia.<br/><br/> Nie usuwa żadnych elementów utworzonych w regionie docelowym.

## <a name="sql-resource-state-after-removing"></a>Stan zasobu SQL po usunięciu

Co się stanie po usunięciu zasobu Azure SQL z kolekcji Move zależy od stanu zasobu, zgodnie z podsumowaniem w tabeli.

**Stan zasobu** | **SQL** 
--- | --- 
**Dodano do kolekcji przenoszenia** | Usuń z kolekcji przenoszenia. 
**Zależności rozwiązane/przygotowywanie oczekujące** | Usuń z kolekcji przenoszenia 
**Przygotowywanie w toku**<br/> (lub wszystkie inne stany w toku)  | Operacja usuwania kończy się niepowodzeniem z powodu błędu. 
**Przygotowywanie nie powiodło się** | Usuń z kolekcji przenoszenia<br/><br/>Usuń wszystkie elementy utworzone w regionie docelowym. 
**Inicjuj oczekiwanie na przeniesienie** |  Usuń z kolekcji przenoszenia<br/><br/>Usuń wszystkie elementy utworzone w regionie docelowym. Baza danych SQL istnieje w tym momencie i zostanie usunięta. 
**Inicjowanie przenoszenia nie powiodło się** | Usuń z kolekcji przenoszenia<br/><br/>Usuń wszystkie elementy utworzone w regionie docelowym. Baza danych SQL istnieje w tym momencie i należy ją usunąć. 
**Oczekiwanie na zatwierdzenie** | Zalecamy odrzucenie przeniesienia, aby najpierw usunąć zasoby docelowe.<br/><br/> Zasób przechodzi z powrotem do stanu **inicjowania przenoszenia oczekującego** i można w dalszym ciągu kontynuować.
**Zatwierdzenie nie powiodło się** | Zalecamy odrzucenie przeniesienia, aby najpierw usunąć zasoby docelowe.<br/><br/> Zasób przechodzi z powrotem do stanu **inicjowania przenoszenia oczekującego** i można w dalszym ciągu kontynuować. 
**Odrzuć ukończone** |  Zasób przechodzi z powrotem do stanu **inicjowania przenoszenia** .<br/><br/> Jest ona usuwana z kolekcji przenoszenia wraz ze wszystkimi elementami utworzonymi w miejscu docelowym, w tym bazami danych SQL. 
**Nie można odrzucić** | Zalecamy odrzucanie ruchów w taki sposób, aby zasoby docelowe zostały usunięte jako pierwsze.<br/><br/> Następnie zasób powróci do stanu **inicjowania przenoszenia oczekujące** i możesz kontynuować od tego momentu. 
**Oczekiwanie na usunięcie źródła** | Usunięte z kolekcji przenoszenia.<br/><br/> Nie usuwa żadnych elementów utworzonych w regionie docelowym. 
**Usuwanie źródła nie powiodło się** | Usunięte z kolekcji przenoszenia.<br/><br/> Nie usuwa żadnych elementów utworzonych w regionie docelowym. 

## <a name="next-steps"></a>Następne kroki

Spróbuj [przenieść maszynę wirtualną](tutorial-move-region-virtual-machines.md) do innego regionu za pomocą przenoszenia zasobów.
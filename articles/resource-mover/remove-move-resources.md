---
title: Usuwanie zasobów z kolekcji przenoszenia w obszarze przenoszenia zasobów platformy Azure
description: Dowiedz się, jak usunąć zasoby z kolekcji Move w obszarze przenoszenia zasobów platformy Azure.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 63548e2bf470c012e0dd8a5f879a51eeb631f453
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459270"
---
# <a name="manage-move-collections-and-resource-groups"></a>Zarządzanie kolekcjami przenoszenia i grupami zasobów

W tym artykule opisano sposób usuwania zasobów z kolekcji przenoszenia lub usuwania kolekcji przenoszenia/grupy zasobów w obszarze [przenoszenia zasobów platformy Azure](overview.md). Kolekcje przenoszenia są używane podczas przenoszenia zasobów platformy Azure między regionami platformy Azure.

## <a name="remove-a-resource-portal"></a>Usuwanie zasobu (Portal)

Zasoby można usunąć w kolekcji przenoszenia w portalu przenoszenia zasobów w następujący sposób:

1. W **różnych regionach** zaznacz wszystkie zasoby, które chcesz usunąć z kolekcji, a następnie wybierz pozycję **Usuń**. 

    ![Przycisk do wybrania do usunięcia](./media/remove-move-resources/portal-select-resources.png)

2. W obszarze **usuwanie zasobów** kliknij pozycję **Usuń**.

    ![Przycisk służący do usuwania zasobów z kolekcji przenoszenia](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>Usuwanie kolekcji przenoszenia/grupy zasobów (Portal)

W portalu można usunąć kolekcję przenoszenia/grupę zasobów.

1. Postępuj zgodnie z instrukcjami podanymi w powyższej procedurze, aby usunąć zasoby z kolekcji. W przypadku usuwania grupy zasobów upewnij się, że nie zawiera ona żadnych zasobów.
2. Usuń kolekcję przenoszenia lub grupę zasobów.  

## <a name="remove-a-resource-powershell"></a>Usuwanie zasobu (PowerShell)

Usuń zasób (w naszym przykładzie maszyn PSDemoVM) z kolekcji przy użyciu programu PowerShell w następujący sposób:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus -Name PSDemoVM
```
**Oczekiwane dane wyjściowe**

![Tekst wyjściowy po usunięciu zasobu z kolekcji przenoszenia](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>Usuwanie kolekcji (program PowerShell)

Usuń całą kolekcję przenoszenia przy użyciu programu PowerShell w następujący sposób:

1. Postępuj zgodnie z powyższymi instrukcjami, aby usunąć zasoby w kolekcji przy użyciu programu PowerShell.
2. Uruchom:

    ```azurepowershell-interactive
    # Remove a resource using the resource ID
    Remove-AzResourceMoverMoveCollection -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus -MoveCollectionName MoveCollection-centralus-westcentralus
    ```
    **Oczekiwane dane wyjściowe**
    
    ![Tekst wyjściowy po usunięciu kolekcji przenoszenia](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Stan zasobu maszyny wirtualnej po usunięciu

Co się stanie po usunięciu zasobu maszyny wirtualnej z kolekcji przenoszenia zależy od stanu zasobu, zgodnie z podsumowaniem w tabeli.

###  <a name="remove-vm-state"></a>Usuń stan maszyny wirtualnej
**Stan zasobu** | **VM** | **Sieć**
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

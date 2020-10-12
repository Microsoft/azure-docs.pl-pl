---
title: Przechowywanie wpisów tajnych w magazynie kluczy w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak przechowywać wpisy tajne w Azure Key Vault i korzystać z nich podczas tworzenia maszyny wirtualnej, formuły lub środowiska.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5714279ef183cb930d643575466dae3d6cb69bba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481650"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Przechowywanie wpisów tajnych w magazynie kluczy w Azure DevTest Labs
W przypadku używania Azure DevTest Labs: hasło dla maszyny wirtualnej z systemem Windows, publicznego klucza SSH dla maszyny wirtualnej z systemem Linux lub osobistego tokenu dostępu w celu sklonowania repozytorium Git za pośrednictwem artefaktu może być konieczne wprowadzenie złożonego hasła. Wpisy tajne są zwykle długie i mają losowe znaki. W związku z tym wprowadzenie ich może być kłopotliwe i niewygodne, szczególnie w przypadku korzystania z tego samego klucza tajnego wiele razy.

Aby rozwiązać ten problem, a także zachować swoje wpisy tajne w bezpiecznym miejscu, DevTest Labs obsługuje przechowywanie wpisów tajnych w [magazynie kluczy platformy Azure](../key-vault/general/overview.md). Gdy użytkownik zapisuje wpis tajny po raz pierwszy, usługa DevTest Labs automatycznie tworzy magazyn kluczy w tej samej grupie zasobów, która zawiera laboratorium i zapisuje wpis tajny w magazynie kluczy. DevTest Labs tworzy osobny Magazyn kluczy dla każdego użytkownika. 

Należy pamiętać, że użytkownik laboratorium musi najpierw utworzyć maszynę wirtualną laboratorium, aby można było utworzyć wpis tajny w magazynie kluczy. Wynika to z faktu, że usługa laboratoryjna DevTest musi skojarzyć użytkownika laboratorium z prawidłowym dokumentem użytkownika, zanim będzie można tworzyć i przechowywać wpisy tajne w magazynie kluczy. 


## <a name="save-a-secret-in-azure-key-vault"></a>Zapisz klucz tajny w Azure Key Vault
Aby zapisać klucz tajny w Azure Key Vault, wykonaj następujące czynności:

1. W menu po lewej stronie wybierz pozycję **moje wpisy tajne** .
2. Wprowadź **nazwę** wpisu tajnego. Ta nazwa zostanie wyświetlona na liście rozwijanej podczas tworzenia maszyny wirtualnej, formuły lub środowiska. 
3. Wprowadź klucz tajny jako **wartość**.

    ![Wpis tajny sklepu](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Użyj klucza tajnego z Azure Key Vault
Jeśli musisz wprowadzić klucz tajny, aby utworzyć maszynę wirtualną, formułę lub środowisko, możesz ręcznie wprowadzić wpis tajny lub wybrać zapisany klucz tajny z magazynu kluczy. Aby użyć wpisu tajnego przechowywanego w magazynie kluczy, wykonaj następujące czynności:

1. Wybierz pozycję **Użyj zapisanego klucza tajnego**. 
2. Wybierz klucz tajny z listy rozwijanej, aby **wybrać wpis tajny**. 

    ![Użyj wpisu tajnego w maszynie wirtualnej](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Używanie wpisu tajnego w szablonie Azure Resource Manager
Możesz określić nazwę tajnego szablonu Azure Resource Manager, który służy do tworzenia maszyny wirtualnej, jak pokazano w następującym przykładzie:

![Używanie klucza tajnego w formule lub środowisku](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie maszyny wirtualnej przy użyciu klucza tajnego](devtest-lab-add-vm.md) 
- [Tworzenie formuły przy użyciu klucza tajnego](devtest-lab-manage-formulas.md)
- [Tworzenie środowiska przy użyciu klucza tajnego](devtest-lab-create-environment-from-arm.md)

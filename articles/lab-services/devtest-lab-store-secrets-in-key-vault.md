---
title: Przechowywanie wpisów tajnych w magazynie kluczy w laboratoriach devtest azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak przechowywać wpisy tajne w magazynie kluczy platformy Azure i używać ich podczas tworzenia maszyny Wirtualnej, formuły lub środowiska.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 0ca36a7081aaf70ee2045ee7586184c89591df16
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461518"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Przechowywanie wpisów tajnych w magazynie kluczy w laboratorium devtest azure
Może być konieczne wprowadzenie złożonego klucza tajnego podczas korzystania z usługi Azure DevTest Labs: hasło dla maszyny Wirtualnej systemu Windows, publiczny klucz SSH dla maszyny Wirtualnej systemu Linux lub token dostępu osobistego w celu sklonowania repozytorium Git za pośrednictwem artefaktu. Sekrety są zwykle długie i mają losowe postacie. Dlatego wprowadzanie ich może być trudne i niewygodne, zwłaszcza jeśli używasz tego samego tajnego wiele razy.

Aby rozwiązać ten problem, a także zachować swoje wpisy tajne w bezpiecznym miejscu, DevTest Labs obsługuje przechowywanie wpisów tajnych w [magazynie kluczy platformy Azure.](../key-vault/general/overview.md) Gdy użytkownik zapisuje klucz tajny po raz pierwszy, usługa DevTest Labs automatycznie tworzy magazyn kluczy w tej samej grupie zasobów, która zawiera laboratorium i przechowuje klucz tajny w magazynie kluczy. DevTest Labs tworzy oddzielny magazyn kluczy dla każdego użytkownika. 

Należy pamiętać, że użytkownik laboratorium będzie musiał najpierw utworzyć maszynę wirtualną laboratorium, zanim będzie mógł utworzyć klucz tajny w magazynie kluczy. Dzieje się tak, ponieważ usługa DevTest Lab musi skojarzyć użytkownika laboratorium z prawidłowym dokumentem użytkownika, zanim będą mogli tworzyć i przechowywać wpisy tajne w magazynie kluczy. 


## <a name="save-a-secret-in-azure-key-vault"></a>Zapisywanie klucza tajnego w usłudze Azure Key Vault
Aby zapisać swój klucz tajny w usłudze Azure Key Vault, wykonaj następujące czynności:

1. Wybierz **pozycję Moje wpisy tajne** w menu po lewej stronie.
2. Wprowadź **nazwę** klucza tajnego. Ta nazwa jest widoczna na liście rozwijanej podczas tworzenia maszyny Wirtualnej, formuły lub środowiska. 
3. Wprowadź klucz tajny jako **wartość**.

    ![Tajemnica sklepu](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Używanie klucza tajnego z usługi Azure Key Vault
Jeśli trzeba wprowadzić klucz tajny, aby utworzyć maszynę wirtualną, formułę lub środowisko, można ręcznie wprowadzić klucz tajny lub wybrać zapisany klucz tajny z magazynu kluczy. Aby użyć klucza tajnego przechowywanego w magazynie kluczy, wykonaj następujące czynności:

1. Wybierz **pozycję Użyj zapisanego klucza tajnego**. 
2. Wybierz swój sekret z listy rozwijanej **Wybierz klucz tajny**. 

    ![Używanie klucza tajnego w maszynie wirtualnej](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Używanie klucza tajnego w szablonie usługi Azure Resource Manager
Nazwę tajnego można określić w szablonie usługi Azure Resource Manager, który jest używany do tworzenia maszyny Wirtualnej, jak pokazano w poniższym przykładzie:

![Używanie klucza tajnego w formule lub środowisku](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie maszyny wirtualnej przy użyciu klucza tajnego](devtest-lab-add-vm.md) 
- [Tworzenie formuły przy użyciu klucza tajnego](devtest-lab-manage-formulas.md)
- [Tworzenie środowiska przy użyciu klucza tajnego](devtest-lab-create-environment-from-arm.md)

---
title: 'ML Studio (klasyczny): zarządzanie obszarami roboczymi — Azure'
description: Zarządzanie dostępem do obszarów roboczych Azure Machine Learning Studio (klasycznymi) oraz wdrażanie usług sieci Web interfejsu API Machine Learning i zarządzanie nimi
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 6f136d0818daca1585de3896305a080ddbabfbe2
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426926"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Zarządzanie obszarem roboczym Azure Machine Learning Studio (klasycznym)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


> [!NOTE]
> Aby uzyskać informacje na temat zarządzania usługami sieci Web w portalu usług sieci Web Machine Learning, zobacz [Zarządzanie usługą sieci Web za pomocą portalu Azure Machine Learning Web Services](manage-new-webservice.md).
> 
> 

W Azure Portal można zarządzać obszarami roboczymi Machine Learning Studio (klasycznymi).



## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby zarządzać obszarem roboczym Studio (klasycznego) w Azure Portal:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora subskrypcji platformy Azure.
2. W polu wyszukiwania w górnej części strony wprowadź "obszary robocze" Machine Learning Studio (klasyczne) ", a następnie wybierz **obszary robocze Machine Learning Studio (klasyczne)**.
3. Kliknij obszar roboczy, który chcesz zarządzać.

Oprócz dostępnych w warstwie Standardowa informacji i opcji zarządzania zasobami można:

- Wyświetl **Właściwości** — ta strona wyświetla informacje o obszarze roboczym i zasobach. można także zmienić subskrypcję i grupę zasobów, z którą jest połączony ten obszar roboczy.
- **Ponowne synchronizowanie kluczy magazynu** — obszar roboczy obsługuje klucze do konta magazynu. Jeśli na koncie magazynu zmienią się klucze, można kliknąć przycisk **Synchronizuj ponownie klucze** , aby zsynchronizować klucze z obszarem roboczym.

Aby zarządzać usługami sieci Web skojarzonymi z tym obszarem roboczym programu Studio (klasycznym), użyj portalu usług sieci Web Machine Learning. Aby uzyskać pełne informacje [, zobacz Zarządzanie usługą sieci Web przy użyciu portalu usług sieci web Azure Machine Learning](manage-new-webservice.md) .

> [!NOTE]
> Aby wdrożyć nowe usługi sieci Web lub zarządzać nimi, musisz mieć przypisaną rolę współautora lub administratora w subskrypcji, w której wdrożono usługę sieci Web. Jeśli zapraszasz innego użytkownika do obszaru roboczego usługi Machine Learning Studio (klasycznego), musisz przypisać go do roli współautor lub administrator w subskrypcji, aby można było wdrożyć usługi sieci Web lub zarządzać nimi. 
> 
>Aby uzyskać więcej informacji na temat ustawiania uprawnień dostępu, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [wdrażaniu Machine Learning z szablonami Azure Resource Manager](deploy-with-resource-manager-template.md). 

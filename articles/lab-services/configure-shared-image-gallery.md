---
title: Konfigurowanie galerii obrazów udostępnionych w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak skonfigurować galerię obrazów udostępnionych w Azure DevTest Labs
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77589321"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurowanie galerii obrazów udostępnionych w usłudze Azure DevTest Labs
DevTest Labs obsługuje teraz funkcję [galerii obrazów udostępnionych](../virtual-machines/windows/shared-image-galleries.md) . Umożliwia użytkownikom laboratorium dostęp do obrazów z udostępnionej lokalizacji podczas tworzenia zasobów laboratoryjnych. Ułatwia ona również tworzenie struktury i organizacji na podstawie niestandardowych zarządzanych obrazów maszyn wirtualnych. Funkcja galerii obrazów udostępnionych obsługuje:

- Zarządza replikacją globalną obrazów
- Przechowywanie wersji i grupowanie obrazów w celu łatwiejszego zarządzania
- Zapewnianie wysokiej dostępności obrazów przy użyciu kont magazynu Strefowo nadmiarowego (ZRS) w regionach, które obsługują strefy dostępności. ZRS zapewnia lepszą odporność na awarie stref.
- Udostępnianie między subskrypcjami, a nawet między dzierżawcami, przy użyciu kontroli dostępu opartej na rolach (RBAC).

Aby uzyskać więcej informacji, zobacz [dokumentację udostępnionej galerii obrazów](../virtual-machines/windows/shared-image-galleries.md). 
 
Jeśli masz dużą liczbę zarządzanych obrazów, które chcesz zachować i chcesz udostępnić je w całej firmie, możesz użyć udostępnionej galerii obrazów jako repozytorium, która ułatwia aktualizowanie i udostępnianie obrazów. Jako właściciel laboratorium możesz dołączyć istniejącą galerię obrazów udostępnionych do laboratorium. Po dołączeniu tej galerii użytkownicy laboratorium mogą tworzyć maszyny z tych najnowszych obrazów. Główną zaletą tej funkcji jest to, że DevTest Labs może teraz korzystać z zalet udostępniania obrazów w laboratoriach, w różnych subskrypcjach i w różnych regionach. 

> [!NOTE]
> Aby dowiedzieć się więcej o kosztach związanych z usługą galerii obrazów udostępnionych, zobacz [rozliczenia dla udostępnionej galerii obrazów](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Zagadnienia do rozważenia
- W danym momencie możesz dołączyć tylko jedną galerię udostępnionych obrazów do laboratorium. Jeśli chcesz dołączyć kolejną galerię, musisz odłączyć istniejącą i dołączyć inną. 
- DevTest Labs obecnie obsługuje tylko obrazy uogólnione galerii obrazów udostępnionych.
- DevTest Labs obecnie nie obsługuje przekazywania obrazów do galerii za pomocą laboratorium. 
- Podczas tworzenia maszyny wirtualnej przy użyciu obrazu udostępnionej galerii obrazów DevTest Labs zawsze używa najnowszej opublikowanej wersji tego obrazu. Jeśli jednak obraz ma wiele wersji, użytkownik może wybrać opcję utworzenia maszyny ze starszej wersji, przechodząc do karty Ustawienia zaawansowane podczas tworzenia maszyny wirtualnej.  
- Mimo że DevTest Labs automatycznie podejmuje najlepszą próbę zagwarantowania, że Galeria obrazów udostępnionych replikuje obrazy do regionu, w którym istnieje laboratorium, nie zawsze jest to możliwe. Aby uniknąć problemów z tworzeniem maszyn wirtualnych z tych obrazów przez użytkowników, upewnij się, że obrazy zostały już zreplikowane do regionu laboratorium ".

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **wszystkie usługi** w menu nawigacji po lewej stronie.
1. Wybierz z listy pozycję **DevTest Labs**.
1. Z listy laboratoriów wybierz **laboratorium**.
1. Wybierz pozycję **Konfiguracja i zasady** w sekcji **Ustawienia** w menu po lewej stronie.
1. W menu po lewej stronie wybierz opcję **udostępnione Galerie obrazów** w obszarze **bazy maszyn wirtualnych** .

    ![Menu udostępnione Galerie obrazów](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Dołączanie istniejącej galerii obrazów udostępnionych do laboratorium przez kliknięcie przycisku **Dołącz** i wybranie galerii z listy rozwijanej.

    ![Dołącz](./media/configure-shared-image-gallery/attach-options.png)
1. Przejdź do dołączonej galerii i skonfiguruj galerię, aby **włączyć lub wyłączyć** obrazy udostępnione na potrzeby tworzenia maszyn wirtualnych. Wybierz z listy galerię obrazów, aby ją skonfigurować. 

    Domyślnie ustawienie **Zezwalaj na używanie wszystkich obrazów jako baz maszyn wirtualnych** jest ustawione na **wartość tak**. Oznacza to, że wszystkie obrazy dostępne w dołączonej galerii obrazów udostępnionych będą dostępne dla użytkownika laboratorium podczas tworzenia nowej maszyny wirtualnej laboratorium. W przypadku konieczności ograniczenia dostępu do niektórych obrazów należy zmienić wartość **Zezwalaj na używanie wszystkich obrazów jako baz maszyn wirtualnych** **, a**następnie wybrać obrazy, które mają być dozwolone podczas tworzenia maszyn wirtualnych, a następnie wybrać przycisk **Zapisz** .

    ![Włącz lub Wyłącz](./media/configure-shared-image-gallery/enable-disable.png)
1. Użytkownicy laboratorium mogą następnie utworzyć maszynę wirtualną za pomocą włączonych obrazów, klikając pozycję **+ Dodaj** i wyszukasz obraz na stronie **Wybierz swoją bazę** .

    ![Użytkownicy laboratorium](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Użyj szablonu Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Dołączanie udostępnionej galerii obrazów do laboratorium
Jeśli używasz szablonu Azure Resource Manager do dołączania udostępnionej galerii obrazów do laboratorium, musisz dodać go w sekcji zasobów szablonu Menedżer zasobów, jak pokazano w następującym przykładzie:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Aby zapoznać się z kompletnym przykładem szablonu Menedżer zasobów, zobacz następujące przykłady szablonów Menedżer zasobów w naszym publicznym repozytorium GitHub: [Konfigurowanie galerii obrazów udostępnionych podczas tworzenia laboratorium](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>Korzystanie z interfejsu API

### <a name="shared-image-galleries---create-or-update"></a>Udostępnione Galerie obrazów — Tworzenie lub aktualizowanie

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Obrazy udostępnionych galerii obrazów — lista 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi tworzenia maszyny wirtualnej przy użyciu obrazu z dołączonej galerii obrazów udostępnionych: [Tworzenie maszyny wirtualnej przy użyciu udostępnionego obrazu z galerii](add-vm-use-shared-image.md)

---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 9a003b5c42a6ef4c699a3768d15ae08f86d56e52
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367298"
---
## <a name="create-a-new-cdn-profile"></a>Tworzenie nowego profilu CDN

Profil usługi CDN jest kontenerem dla punktów końcowych usługi CDN i określa warstwę cenową.

1. W Azure Portal wybierz pozycję **Utwórz zasób** (w lewym górnym rogu). Zostanie wyświetlone okienko **Nowy**.
   
1. Wyszukaj i wybierz pozycję **CDN**, a następnie wybierz pozycję **Utwórz**:
   
    ![Wybierz zasób usługi CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Zostanie wyświetlone okienko **profil usługi CDN** .

1. Wprowadź następujące wartości:
   
    | Ustawienie  | Wartość |
    | -------- | ----- |
    | **Nazwa** | W obszarze Nazwa profilu Wprowadź nazwę *CDN-profile-123* . |
    | **Subskrypcja** | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | **Grupa zasobów** | Wybierz pozycję **Utwórz nową** i wprowadź *CDNQuickstart-RG* dla nazwy grupy zasobów lub wybierz opcję **Użyj istniejącej** , a następnie wybierz pozycję *CDNQuickstart-RG* , jeśli istnieje już Grupa. | 
    | **Lokalizacja grupy zasobów** | Wybierz z listy rozwijanej lokalizację znajdującą się obok siebie. |
    | **Warstwa cenowa** | Wybierz **standardową opcję Akamai** z listy rozwijanej. (Czas wdrożenia dla warstwy Akamai jest około jednej minuty. Warstwa firmy Microsoft zajmie około 10 minut, a warstwy Verizon zajmują około 30 minut. |
    | **Utwórz teraz nowy punkt końcowy CDN** | Pozostaw niezaznaczone. |  
   
    ![Nowy profil usługi CDN](./media/cdn-create-profile/cdn-new-profile.png)

1. Wybierz pozycję **Utwórz**, aby utworzyć profil.


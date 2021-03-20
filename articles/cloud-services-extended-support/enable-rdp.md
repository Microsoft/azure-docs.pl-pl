---
title: Zastosuj Pulpit zdalny w Cloud Services (obsługa rozszerzona)
description: Włącz Pulpit zdalny dla Cloud Services (obsługa rozszerzona)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 53f873013a6f16ce5a28ee5d915afa556057f643
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744418"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>Stosowanie rozszerzenia Pulpit zdalny do usługi Azure Cloud Services (obsługa rozszerzona)

Azure Portal korzysta z rozszerzenia pulpitu zdalnego, aby włączyć pulpit zdalny nawet po wdrożeniu aplikacji. Ustawienia pulpitu zdalnego dla usługi w chmurze umożliwiają włączenie pulpitu zdalnego, zaktualizowanie konta administratora lokalnego, wybranie certyfikatów używanych w ramach uwierzytelniania i ustawienie daty wygaśnięcia dla tych certyfikatów. 

## <a name="apply-remote-desktop--extension"></a>Zastosuj rozszerzenie Pulpit zdalny
1. Przejdź do usługi w chmurze, dla której chcesz włączyć pulpit zdalny dla i wybierz pozycję **"pulpit zdalny"** w okienku nawigacji po lewej stronie.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Obraz pokazuje wybranie opcji Pulpit zdalny w Azure Portal":::

2. Wybierz pozycję **Dodaj**.
3. Wybierz role, aby włączyć pulpit zdalny dla programu.
4. Wypełnij pola wymagane w polu Nazwa użytkownika, hasło, wygaśnięcie i certyfikat (niewymagane).

    :::image type="content" source="media/remote-desktop-2.png" alt-text="Obraz przedstawiający wprowadzanie informacji wymaganych do nawiązania połączenia z pulpitem zdalnym.":::

5. Po zakończeniu wybierz pozycję **Zapisz**. Potrwa to chwilę, zanim wystąpienia roli będą gotowe do odbierania połączeń.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>Nawiązywanie połączenia z wystąpieniami roli z włączonym Pulpit zdalny
Po włączeniu pulpitu zdalnego na rolach można zainicjować połączenie bezpośrednio z poziomu Azure Portal.

1. Kliknij pozycję **role i wystąpienia** , aby otworzyć ustawienia wystąpienia.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="Obraz pokazuje wybranie opcji role i Instances w bloku konfiguracja.":::

2. Wybierz wystąpienie roli z skonfigurowanym pulpitem zdalnym.
3. Kliknij przycisk **Połącz** , aby pobrać plik połączenia pulpitu zdalnego.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="Obraz przedstawia wybieranie wystąpienia roli procesu roboczego w Azure Portal.":::
    
4. Otwórz plik, aby nawiązać połączenie z wystąpieniem roli.


## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).
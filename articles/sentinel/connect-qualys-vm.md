---
title: Połącz dane zarządzania lukami w zabezpieczeniach Qualys z platformą Azure — wskaźnikiem | Microsoft Docs
description: Dowiedz się, jak połączyć dane zarządzania lukami w zabezpieczeniach Qualys z platformą Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 34f2cfa06cbdbb75b8fd610cd1f76fb33dde4cde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87059851"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>Połącz maszynę wirtualną z programem Qualys z platformą Azure wskaźnikiem przy użyciu funkcji platformy Azure

> [!IMPORTANT]
> Łącznik danych maszyny wirtualnej Qualys na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Łącznik Qualysego zarządzania lukami w zabezpieczeniach pozwala łatwo połączyć wszystkie dzienniki rozwiązań zabezpieczeń [maszyn wirtualnych Qualys](https://www.qualys.com/apps/vulnerability-management/) z platformą Azure, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i poprawić badanie. Integracja między maszyną wirtualną Qualys i wskaźnikiem "Azure" wykorzystuje Azure Functions do ściągania danych dzienników przy użyciu interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-qualys-vm"></a>Konfigurowanie i łączenie maszyny wirtualnej Qualys

Azure Functions może zintegrować i ściągać zdarzenia i dzienniki bezpośrednio z maszyny wirtualnej Qualys i przekazywać je do usługi Azure wskaźnikowej.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję Łącznik zarządzania lukami w **zabezpieczeniach Qualys** .

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie zarządzania lukami w **zabezpieczeniach Qualys** .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w tabeli **QualysHostDetection_CL** .

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z maszyną wirtualną Qualys z platformą Azure, korzystając z aplikacji funkcji platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.

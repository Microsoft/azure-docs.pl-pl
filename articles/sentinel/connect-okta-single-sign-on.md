---
title: Łączenie usługi okta jednego Sign-On z danymi na platformie Azure Microsoft Docs
description: Dowiedz się, jak połączyć usługi okta pojedyncze Sign-On z danymi na platformie Azure.
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
ms.openlocfilehash: e76aea8a3fc59827664900a6d5686e2e725e258d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093119"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Połącz usługi okta pojedyncze Sign-On z funkcją wskaźnikową platformy Azure przy użyciu funkcji platformy Azure

> [!IMPORTANT]
> Łącznik usługi okta Single Sign-On Data na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Łącznik usługi okta Single Sign-On (SSO) umożliwia łatwe łączenie dzienników rozwiązań zabezpieczeń [pojedynczego Sign-On (SSO)](https://www.okta.com/products/single-sign-on/) w usłudze usługi okta z platformą Azure — do wyświetlania pulpitów nawigacyjnych, tworzenia alertów niestandardowych i ulepszania badania. Integracja między usługi okta pojedynczym Sign-On i wskaźnikiem na platformie Azure to użycie Azure Functions do ściągania danych dzienników przy użyciu interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-okta-single-sign-on"></a>Konfigurowanie i łączenie usługi okta pojedyncze Sign-On

Azure Functions umożliwia integrację i ściąganie zdarzeń i dzienników bezpośrednio z usługi okta pojedynczego Sign-On i przekazywanie ich do usługi Azure wskaźnikowej.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję **usługi okta Single Sign-on** Connector.

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami na stronie logowania jednokrotnego **usługi okta** .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w tabeli **Okta_CL** .

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia usługi okta pojedynczej Sign-On z platformą Azure z użyciem aplikacji funkcji platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.


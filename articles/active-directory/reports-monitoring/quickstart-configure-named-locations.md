---
title: Konfigurowanie nazwanych lokalizacji w usłudze Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak konfigurować nazwane lokalizacje.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 897762e523a2d20445c3a25e612cf138e021f633
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739723"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Szybki start: konfigurowanie nazwanych lokalizacji w usłudze Azure Active Directory

Za pomocą nazwanych lokalizacji możesz oznaczać etykietą zakresy zaufanych adresów IP w Twojej organizacji. Usługa Azure AD używa nazwanych lokalizacji w następujących celach:
- Wykrywaj fałszywie dodatnie [wykrycia zagrożeń](concept-risk-events.md). Logowanie z zaufanej lokalizacji obniża ryzyko użytkownika związane z logowaniem.   
- Konfigurowanie [dostępu warunkowego opartego na lokalizacji](../conditional-access/location-condition.md).

W tym przewodniku Szybki start dowiesz się, jak skonfigurować nazwane lokalizacje w swoim środowisku.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

* Dzierżawa usługi Azure AD. Zarejestruj się, aby skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Użytkownik będący administratorem globalnym dzierżawy.
* Zakres adresów IP, który jest uznany i wiarygodny w Twojej organizacji. Zakres adresów IP musi mieć format **CIDR (Classless Interdomain Routing)**.

## <a name="configure-named-locations"></a>Konfigurowanie nazwanych lokalizacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W lewym okienku wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **dostęp warunkowy** z sekcji **zabezpieczenia** .

    ![Karta dostęp warunkowy](./media/quickstart-configure-named-locations/entrypoint.png)

3. Na stronie **Dostęp warunkowy** wybierz pozycję **Lokalizacje nazwane**, a następnie **Nowa lokalizacja**.

    ![Nazwana lokalizacja](./media/quickstart-configure-named-locations/namedlocation.png)

6. Wypełnij formularz na nowej stronie. 

   * W polu **Nazwa** wpisz nazwę dla nazwanej lokalizacji.
   * W polu **Zakresy adresów IP** wpisz zakres adresów IP w formacie CIDR.  
   * Kliknij przycisk **Utwórz**.
    
     ![Blok Nowy](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz:

- [Lokalizacja jako warunek dostępu warunkowego](../conditional-access/concept-conditional-access-conditions.md#locations).
- [Raport dotyczący ryzykownych logowań](concept-risky-sign-ins.md)  

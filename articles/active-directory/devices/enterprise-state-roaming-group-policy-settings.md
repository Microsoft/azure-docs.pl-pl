---
title: Ustawienia zasady grupy i MDM dla usługi ESR — Azure Active Directory
description: Ustawienia zarządzania dla Enterprise State Roaming
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab24b3113f9dc69b8f3907037e228ba212a03106
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85252937"
---
# <a name="group-policy-and-mdm-settings"></a>Ustawienia zasady grupy i zarządzania urządzeniami przenośnymi

Te zasady grupy i ustawienia zarządzania urządzeniami przenośnymi (MDM) są używane tylko na urządzeniach należących do firmy, ponieważ te zasady są stosowane do całego urządzenia użytkownika. Zastosowanie zasad zarządzania urządzeniami przenośnymi w celu wyłączenia synchronizacji ustawień dla urządzenia osobistego użytkownika nie wpłynie negatywnie na korzystanie z tego urządzenia. Zasady mogą również wpływać na inne konta użytkowników na urządzeniu.

Przedsiębiorstwa, które chcą zarządzać roamingiem dla urządzeń osobistych (niezarządzanych), mogą używać Azure Portal do włączania lub wyłączania roamingu zamiast używania zasady grupy lub MDM.
W poniższych tabelach opisano dostępne ustawienia zasad.

> [!NOTE]
> Ten artykuł ma zastosowanie do starszej przeglądarki opartej na języku HTML Microsoft Edge, która została uruchomiona z systemem Windows 10 w lipcu 2015. Artykuł nie ma zastosowania do nowej przeglądarki opartej na formacie chromu Microsoft Edge wydanej 15 stycznia 2020. Aby uzyskać więcej informacji na temat zachowania synchronizacji dla nowej przeglądarki Microsoft Edge, zobacz artykuł [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>Ustawienia zarządzania urządzeniami przenośnymi

Ustawienia zasad MDM dotyczą zarówno systemu Windows 10, jak i Windows 10 Mobile.  Obsługa systemu Windows 10 Mobile istnieje tylko w przypadku roamingu konto Microsoft na podstawie konta użytkownika w usłudze OneDrive. Zapoznaj się z tematami [urządzenia i punkty końcowe,](enterprise-state-roaming-windows-settings-reference.md) Aby uzyskać szczegółowe informacje na temat urządzeń obsługiwanych w przypadku synchronizacji opartej na usłudze Azure AD.

| Nazwa | Opis |
| --- | --- |
| Zezwalaj na połączenie konta Microsoft |Zezwala użytkownikom na uwierzytelnianie przy użyciu konto Microsoft na urządzeniu |
| Zezwalaj na synchronizowanie moich ustawień |Umożliwia użytkownikom roaming ustawień systemu Windows i danych aplikacji. Wyłączenie tych zasad spowoduje wyłączenie synchronizacji oraz tworzenie kopii zapasowych na urządzeniach przenośnych |

## <a name="group-policy-settings"></a>Ustawienia zasad grupy

Ustawienia zasad grupy stosują się do urządzeń z systemem Windows 10, które są przyłączone do domeny Active Directory. Tabela zawiera również starsze ustawienia, które pojawią się w celu zarządzania ustawieniami synchronizacji, ale które nie działają dla Enterprise State Roaming dla systemu Windows 10, które są wymienione w opisie "nie używaj".

Te ustawienia znajdują się w: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Nazwa | Opis |
| --- | --- |
| Konta: Blokuj konta Microsoft |To ustawienie zasad uniemożliwia użytkownikom dodawanie nowych kont Microsoft na tym komputerze |
| Nie Synchronizuj |Uniemożliwia użytkownikom roaming ustawień systemu Windows i danych aplikacji |
| Nie Synchronizuj personalizacji |Wyłącza synchronizację grupy motywy |
| Nie Synchronizuj ustawień przeglądarki |Wyłącza synchronizację grupy programu Internet Explorer |
| Nie Synchronizuj haseł |Wyłącza synchronizację grupy haseł |
| Nie Synchronizuj innych ustawień systemu Windows |Wyłącza synchronizację z inną grupą ustawień systemu Windows |
| Nie Synchronizuj personalizacji pulpitu |Nie należy używać; nie ma wpływu |
| Nie Synchronizuj w połączeniach taryfowych |Wyłącza roaming dla połączeń taryfowych, takich jak sieć komórkowa 3G |
| Nie Synchronizuj aplikacji |Nie należy używać; nie ma wpływu |
| Nie Synchronizuj ustawień aplikacji |Wyłącza roaming danych aplikacji |
| Nie Synchronizuj ustawień uruchomieniowych |Nie należy używać; nie ma wpływu |

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem, zobacz [Omówienie usługi Enterprise State roaming](enterprise-state-roaming-overview.md).

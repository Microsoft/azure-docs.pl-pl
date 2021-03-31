---
title: Co to jest roaming stanu przedsiębiorstwa w Azure Active Directory?
description: Enterprise State Roaming zapewnia użytkownikom ujednolicone środowisko na urządzeniach z systemem Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77194283"
---
# <a name="what-is-enterprise-state-roaming"></a>Co to jest roaming stanu dla przedsiębiorstw?

W systemie Windows 10 Użytkownicy [usługi Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) mogą bezpiecznie synchronizować ustawienia użytkownika i dane ustawień aplikacji z chmurą. Enterprise State Roaming zapewnia użytkownikom ujednolicone środowisko na urządzeniach z systemem Windows i skraca czas wymagany do skonfigurowania nowego urządzenia. Enterprise State Roaming działa podobnie do standardowej [synchronizacji ustawień klienta](https://go.microsoft.com/fwlink/?linkid=2015135) , która została wprowadzona po raz pierwszy w systemie Windows 8. Ponadto Enterprise State Roaming oferuje:

* **Rozdzielenie danych firmowych i konsumenckich** — organizacje mają kontrolę nad swoimi danymi, a dane firmowe nie są używane na koncie w chmurze klienta lub w danych konsumenta na koncie w chmurze przedsiębiorstwa.
* **Ulepszone zabezpieczenia** — dane są szyfrowane automatycznie przed opuszczeniem urządzenia z systemem Windows 10 przy użyciu usługi Azure Rights Management (Azure RMS), a dane pozostają zaszyfrowane w chmurze. Cała zawartość pozostaje zaszyfrowana w chmurze, z wyjątkiem przestrzeni nazw, takich jak nazwy ustawień i nazwy aplikacji systemu Windows.  
* **Lepsze zarządzanie i monitorowanie** — zapewnia kontrolę nad tym, kto synchronizuje ustawienia w organizacji oraz na urządzeniach korzystających z integracji portalu usługi Azure AD. 

Enterprise State Roaming jest dostępny w wielu regionach świadczenia usługi Azure. Zaktualizowaną listę dostępnych regionów można znaleźć na stronie [usługi platformy Azure według regionów](https://azure.microsoft.com/regions/#services) w obszarze Azure Active Directory.

| Artykuł | Opis |
| --- | --- |
| [Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming jest dostępna dla każdej organizacji z subskrypcją Azure Active Directory Premium (Azure AD). Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji usługi Azure AD, zobacz stronę [produktu usługi Azure AD](https://azure.microsoft.com/services/active-directory) . |
| [Roaming ustawień i danych — często zadawane pytania](enterprise-state-roaming-faqs.md) |W tym artykule znajdują się odpowiedzi na pytania, które mogą być dostępne dla administratorów IT dotyczących ustawień i synchronizacji danych aplikacji. |
| [Zasady grupy i ustawienia MDM dla synchronizacji ustawień](enterprise-state-roaming-group-policy-settings.md) |System Windows 10 udostępnia ustawienia zasad zasady grupy i zarządzania urządzeniami przenośnymi (MDM) w celu ograniczenia synchronizacji ustawień. |
| [Informacje dotyczące ustawień roamingu w systemie Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Lista ustawień, które będą przekazywane i/lub tworzone w systemie Windows 10. |
| [Rozwiązywanie problemów](enterprise-state-roaming-troubleshooting.md) |W tym artykule przedstawiono kilka podstawowych kroków dotyczących rozwiązywania problemów i zawiera listę znanych problemów. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat włączania roamingu stanu przedsiębiorstwa, zobacz [Włączanie roamingu stanu przedsiębiorstwa](enterprise-state-roaming-enable.md).

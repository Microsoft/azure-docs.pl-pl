---
title: Rozwiązywanie problemów z instalacją Azure AD Connect | Microsoft Docs "
description: Ten temat zawiera instrukcje dotyczące rozwiązywania problemów z instalacją Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89275865"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Rozwiązywanie problemów: problemy z instalowaniem Azure AD Connect

## <a name="recommended-steps"></a>**Zalecane czynności**
Sprawdź, który [Azure AD Connect typ instalacji](./how-to-connect-install-select-installation.md) jest odpowiedni dla Ciebie. Jeśli spełniasz kryteria instalacji ekspresowej, zdecydowanie zalecamy przeprowadzenie instalacji ekspresowej. Instalacja ekspresowa zapewnia minimalne opcje potrzebne do zakończenia instalacji, dlatego istnieje mniejsze prawdopodobieństwo wystąpienia problemów. 

Jeśli jednak nie spełnisz kryteriów instalacji ekspresowej i musisz przeprowadzić instalację niestandardową, możesz wykonać kilka najlepszych rozwiązań, aby uniknąć typowych problemów. Tylko w przypadku uproszczenia są wymienione tutaj tylko opcje selektywne:

* Upewnij się, że jesteś administratorem na komputerze, na którym instalujesz aplikację AAD Connect. Zaloguj się na komputerze przy użyciu tych samych poświadczeń administratora.

* Zezwalaj na ustawienie domyślne na poniższej stronie, z wyjątkiem opcji "Użyj istniejącej SQL Server", jeśli chcesz użyć istniejących SQL Server. Poniżej przedstawiono [więcej informacji](./how-to-connect-install-custom.md) na temat używania niestandardowych opcji instalacji. 

    ![Użyj istniejących SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na poniższej stronie wybierz opcję "Utwórz nowe konto usługi AD", aby uniknąć problemów z uprawnieniami istniejącego konta.

    ![Konto lasu usługi AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Typowe problemy**

* [Problemy z łącznością z Active Directory lokalnymi](./reference-connect-adconnectivitytools.md).

* [Problemy z łącznością z Azure Active Directoryami online](./tshoot-connect-connectivity.md).

* [Problemy z uprawnieniami Active Directory lokalnych](./how-to-connect-configure-ad-ds-connector-account.md).

## <a name="recommended-documents"></a>**Zalecane dokumenty**
* [Wymagania wstępne dotyczące programu Azure AD Connect](./how-to-connect-install-prerequisites.md)
* [Wybieranie typu instalacji do użycia dla programu Azure AD Connect](./how-to-connect-install-select-installation.md)
* [Rozpoczynanie pracy z programem Azure AD Connect przy użyciu ustawień ekspresowych](./how-to-connect-install-express.md)
* [Niestandardowa instalacja programu Azure AD Connect](./how-to-connect-install-custom.md)
* [Azure AD Connect: uaktualnianie z wcześniejszej wersji do najnowszej](./how-to-upgrade-previous-version.md)
* [Azure AD Connect: co to jest serwer przejściowy?](./plan-connect-topologies.md#staging-server)
* [Co to jest moduł ADConnectivityTool programu PowerShell?](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>Następne kroki
- [Azure AD Connect synchronizację](how-to-connect-sync-whatis.md).
- [Co to jest tożsamość hybrydowa?](whatis-hybrid-identity.md)
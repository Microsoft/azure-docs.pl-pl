---
title: Rozwiązywanie problemów z atrybutem, który nie jest synchronizowany w Azure AD Connect | Microsoft Docs "
description: Ten temat zawiera instrukcje dotyczące rozwiązywania problemów z synchronizacją atrybutów przy użyciu zadania rozwiązywania problemów.
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
ms.openlocfilehash: 0a16e989a6da8daa4a290c7eaa4363eef09c9749
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85356342"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Rozwiązywanie problemów z atrybutem, który nie jest synchronizowany w Azure AD Connect

## <a name="recommended-steps"></a>**Zalecane czynności**

Przed badaniem problemów z synchronizacją atrybutów, przyjrzyjmy się procesowi synchronizacji **Azure AD Connect** :

  ![Proces synchronizacji Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Przestrzeń łącznika, tabela w bazie danych.
* **MV:** Metaverse, tabela w bazie danych.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Kroki synchronizacji**

* Importuj z usługi AD: obiekty Active Directory są przyłączone do usług AD CS.

* Importuj z usługi AAD: obiekty Azure Active Directory są umieszczane w usłudze AAD CS.

* Synchronizacja: **reguły synchronizacji ruchu przychodzącego** i **reguły synchronizacji ruchu wychodzącego** są uruchamiane w kolejności z niższym do wyższego poziomu. Aby wyświetlić reguły synchronizacji, można przejść do **edytora reguł synchronizacji** z aplikacji klasycznych. **Reguły synchronizacji ruchu przychodzącego** są w danych z cs do MV. **Reguły synchronizacji wychodzącej** przenosiją dane z mV do cs.

* Eksportuj do usługi AD: po uruchomieniu synchronizacji obiekty są eksportowane z usług AD CS do **Active Directory**.

* Eksportuj do usługi AAD: po uruchomieniu synchronizacji obiekty są eksportowane z usługi AAD CS do **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Badanie krok po kroku**

* Rozpocznie wyszukiwanie od **Metaverse** i Przyjrzyjmy się mapowaniu atrybutów z lokalizacji źródłowej do docelowej.

* Uruchom **Synchronization Service Manager** z aplikacji klasycznych, jak pokazano poniżej:

  ![Synchronization Service Manager uruchamiania](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* Na **Synchronization Service Manager**wybierz pozycję **Wyszukiwanie Metaverse**, wybierz pozycję **zakres według typu obiektu**, wybierz obiekt przy użyciu atrybutu, a następnie kliknij przycisk **Wyszukaj** .

  ![Metaverse — wyszukiwanie](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Kliknij dwukrotnie obiekt znaleziony w wyszukiwaniu **Metaverse** , aby wyświetlić wszystkie jego atrybuty. Możesz kliknąć kartę **Łączniki** , aby przyjrzeć się odpowiadającemu obiektowi we wszystkich **miejscach łączników**.

  ![Łączniki obiektów Metaverse](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Kliknij dwukrotnie **łącznik Active Directory** , aby wyświetlić atrybuty **przestrzeni łącznika** . Kliknij przycisk **Podgląd** , w następującym oknie dialogowym kliknij przycisk **Generuj Podgląd** .

  ![Atrybuty przestrzeni łącznika](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Teraz kliknij kolejno opcje **Importuj przepływ**atrybutów, który pokazuje przepływ atrybutów z **Active Directory miejsca łącznika** do **Metaverse**. Kolumna **reguły synchronizacji** pokazuje, która **reguła synchronizacji** przyczyniła ten atrybut. Kolumna **Źródło danych** pokazuje atrybuty **obszaru łącznika**. Kolumna **atrybutu Metaverse** zawiera atrybuty w obiekcie **Metaverse**. Ten atrybut nie jest synchronizowany w tym miejscu. Jeśli nie znajdziesz tutaj atrybutu, nie jest on mapowany i trzeba utworzyć nową niestandardową **regułę synchronizacji** w celu zamapowania atrybutu.

  ![Atrybuty przestrzeni łącznika](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Kliknij **przepływ atrybutu Export** w lewym okienku, aby wyświetlić przepływ **atrybutów z** wycofywania do **Active Directory obszaru łącznika** przy użyciu **reguł synchronizacji danych wychodzących**.

  ![Atrybuty przestrzeni łącznika](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Podobnie można wyświetlić obiekt **obszaru łącznika Azure Active Directory** i wygenerować **Podgląd** do wyświetlania przepływu atrybutów z **Metaverse** do **obszaru łącznika i na** odwrót, w ten sposób można zbadać dlaczego atrybut nie jest synchronizowany.

## <a name="recommended-documents"></a>**Zalecane dokumenty**
* [Azure AD Connect Sync: koncepcje techniczne](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Synchronizacja Azure AD Connect: zrozumienie architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect Sync: wyjaśnienie aprowizacji deklaracyjnej](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect Sync: wyjaśnienie wyrażeń aprowizacji deklaracyjnej](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Synchronizacja programu Azure AD Connect: opis konfiguracji domyślnej](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure AD Connect Sync: zrozumienie użytkowników, grup i kontaktów](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Synchronizacja Azure AD Connect: atrybuty cienia](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Następne kroki

- [Azure AD Connect synchronizację](how-to-connect-sync-whatis.md).
- [Co to jest tożsamość hybrydowa?](whatis-hybrid-identity.md).

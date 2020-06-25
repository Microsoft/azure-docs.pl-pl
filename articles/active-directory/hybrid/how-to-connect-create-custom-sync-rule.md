---
title: Jak dostosować regułę synchronizacji w programie Azure AD Connect | Microsoft Docs "
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
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75300824d94ecbd77f426539ce99da6caaa2e27a
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85359997"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Jak dostosować regułę synchronizacji

## <a name="recommended-steps"></a>**Zalecane czynności**

Aby edytować lub utworzyć nową regułę synchronizacji, można użyć edytora reguł synchronizacji. Musisz być zaawansowanym użytkownikiem, aby wprowadzać zmiany w regułach synchronizacji. Wszelkie niewłaściwe zmiany mogą spowodować usunięcie obiektów z katalogu docelowego. Zapoznaj się z [zalecanymi dokumentami](#recommended-documents) , aby uzyskać wiedzę na temat reguł synchronizacji. Aby zmodyfikować regułę synchronizacji, wykonaj następujące czynności:

* Uruchom Edytor synchronizacji z menu aplikacji na pulpicie, jak pokazano poniżej:

    ![Menu edytora reguł synchronizacji](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Aby dostosować domyślną regułę synchronizacji, Sklonuj istniejącą regułę, klikając przycisk "Edytuj" w Edytorze reguł synchronizacji, co spowoduje utworzenie kopii standardowej reguły domyślnej i jej wyłączenie. Zapisz sklonowaną regułę z pierwszeństwem mniejszym niż 100.  Pierwszeństwo określa, jaka reguła WINS (niższa wartość liczbowa) występuje w przypadku konfliktu przepływu atrybutów.

    ![Edytor reguł synchronizacji](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Przy modyfikowaniu określonego atrybutu najlepiej zachować tylko atrybut modyfikujący reguły klonowania.  Następnie Włącz regułę domyślną, tak aby zmodyfikowany atrybut pochodzi ze sklonowanej reguły, a inne atrybuty są wybierane z domyślnej reguły standardowej. 

* Należy zwrócić uwagę, że w przypadku, gdy obliczona wartość zmodyfikowanego atrybutu ma wartość NULL w sklonowanej regule i nie ma wartości NULL w domyślnej regule standardowej, a wartość nie będzie równa null i zastąpi wartość NULL. Jeśli nie chcesz, aby wartość NULL była zastępowana wartością niezerową, przypisz AuthoritativeNull do sklonowanej reguły.

* Aby zmodyfikować regułę **ruchu wychodzącego** , Zmień filtr w Edytorze reguł synchronizacji.

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

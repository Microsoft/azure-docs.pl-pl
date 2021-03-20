---
title: Jak dostosować regułę synchronizacji w programie Azure AD Connect | Microsoft Docs "
description: Informacje dotyczące edytowania lub tworzenia nowej reguły synchronizacji przy użyciu edytora reguł synchronizacji.
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
ms.openlocfilehash: e2bb86988454141dc692b4a9967997c4ff7574a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530492"
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
* [Azure AD Connect Sync: koncepcje techniczne](./how-to-connect-sync-technical-concepts.md)
* [Synchronizacja Azure AD Connect: zrozumienie architektury](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect Sync: wyjaśnienie aprowizacji deklaracyjnej](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect Sync: wyjaśnienie wyrażeń aprowizacji deklaracyjnej](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Synchronizacja programu Azure AD Connect: opis konfiguracji domyślnej](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect Sync: zrozumienie użytkowników, grup i kontaktów](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Synchronizacja Azure AD Connect: atrybuty cienia](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Następne kroki
- [Azure AD Connect synchronizację](how-to-connect-sync-whatis.md).
- [Co to jest tożsamość hybrydowa?](whatis-hybrid-identity.md).
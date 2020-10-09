---
title: Operacje Synchronization Service Manager Azure AD Connect | Microsoft Docs
description: Zapoznaj się z kartą operacje w Synchronization Service Manager Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64ab5901ad54e37fc05d7da757ce7f31d42fe3b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85357107"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Korzystanie z karty operacje Service Manager synchronizacji

![Service Manager synchronizacji](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

Na karcie operacje są wyświetlane wyniki najnowszych operacji. Ta karta jest kluczem do zrozumienia i rozwiązywania problemów.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Informacje widoczne na karcie operacje
Górna połowa przedstawia wszystkie uruchomienia w kolejności chronologicznej. Domyślnie dziennik operacji przechowuje informacje o ciągu ostatnich siedmiu dni, ale to ustawienie można zmienić przy użyciu [harmonogramu](how-to-connect-sync-feature-scheduler.md). Chcesz wyszukać dowolny przebieg, który nie pokazuje stanu sukcesu. Sortowanie można zmienić, klikając nagłówki.

Kolumna **stan** jest najważniejszymi informacjami i pokazuje najbardziej poważny problem w przypadku uruchomienia. Oto krótkie podsumowanie najbardziej typowych stanów w kolejności, w której ma zostać zbadane (gdzie * wskazuje kilka możliwych ciągów błędów).

| Stan | Komentarz |
| --- | --- |
| przerwać\* |Nie można ukończyć przebiegu. Na przykład jeśli system zdalny nie działa i nie można się z nim skontaktować. |
| Zatrzymano — limit błędów |Występuje więcej niż 5 000 błędów. Przebieg został automatycznie zatrzymany z powodu dużej liczby błędów. |
| ukończono — \* Błędy |Ukończono przebieg, ale wystąpiły błędy (mniej niż 5 000), które powinny zostać zbadane. |
| ukończone — \* ostrzeżenia |Przebieg został ukończony, ale niektóre dane nie są w oczekiwanym stanie. Jeśli występują błędy, ten komunikat jest zazwyczaj tylko objawem. Dopóki nie zostaną rozkierowane błędy, nie należy badać ostrzeżeń. |
| powodzenie |Brak problemów. |

Po wybraniu wiersza Dolna aktualizacja pokazuje szczegóły tego uruchomienia. Na końcu z lewej strony możesz wyświetlić listę z informacją o **kroku #**. Ta lista pojawia się tylko wtedy, gdy w lesie istnieje wiele domen, w których każda domena jest reprezentowana przez krok. Nazwę domeny można znaleźć pod **partycją**nagłówka. W obszarze **Statystyka synchronizacji**można znaleźć więcej informacji na temat liczby przetworzonych zmian. Możesz kliknąć linki, aby uzyskać listę zmienionych obiektów. Jeśli masz obiekty z błędami, te błędy są wyświetlane w obszarze **Błędy synchronizacji**.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z obiektem, który nie jest synchronizowany](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji Azure AD Connect](how-to-connect-sync-whatis.md) .

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

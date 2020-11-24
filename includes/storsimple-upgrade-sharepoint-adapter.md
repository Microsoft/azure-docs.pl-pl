---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3467a5d5daa300f82c7b81641ab7e262259d9285
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554418"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Uaktualnij program SharePoint 2010 do programu SharePoint 2013, a następnie zainstaluj adapter StorSomple dla programu SharePoint
> [!IMPORTANT]
> Wszystkie pliki, które zostały wcześniej przeniesione do magazynu zewnętrznego za pośrednictwem SPZ, nie będą dostępne do momentu zakończenia uaktualniania i włączenia funkcji SPZ. Aby ograniczyć wpływ na użytkowników, należy przeprowadzić uaktualnienie lub ponowną instalację podczas planowanego okna obsługi.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Aby uaktualnić SharePoint 2010 do programu SharePoint 2013, a następnie zainstalować kartę
1. W farmie programu SharePoint 2010 Zwróć uwagę na ścieżkę magazynu obiektów BLOB dla zewnętrznych obiektów blob i baz danych zawartości, dla których jest włączona funkcja SPZ. 
2. Zainstaluj i skonfiguruj nową farmę programu SharePoint 2013. 
3. Przenoszenie baz danych, aplikacji i kolekcji witryn z farmy programu SharePoint 2010 do nowej farmy programu SharePoint 2013. Aby uzyskać instrukcje, przejdź do [omówienia procesu uaktualniania do programu SharePoint 2013](/SharePoint/upgrade-and-update/overview-of-the-upgrade-process).
4. Zainstaluj adapter StorSimple dla programu SharePoint w nowej farmie. Przejdź do procedury [instalacji adaptera StorSimple dla programu SharePoint](#install-the-storsimple-adapter-for-sharepoint) .
5. Korzystając z informacji zanotowanych w kroku 1, Włącz strukturę SPZ dla tego samego zestawu baz danych zawartości i podaj tę samą ścieżkę magazynu obiektów BLOB, która została użyta w instalacji programu SharePoint 2010. Przejdź do pozycji [Konfigurowanie SPZ](#configure-rbs) dla procedur. Po wykonaniu tego kroku wcześniej zewnętrzne pliki powinny być dostępne z nowej farmy. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Uaktualnianie adaptera StorSimple dla programu SharePoint
> [!IMPORTANT]
> Należy zaplanować to uaktualnienie w trakcie zaplanowanego okna obsługi z następujących powodów:
> 
> * Wcześniej zewnętrzna zawartość nie będzie dostępna do momentu ponownego zainstalowania karty.
> * Każda zawartość przekazana do lokacji po odinstalowaniu poprzedniej wersji adaptera StorSimple dla programu SharePoint, ale przed zainstalowaniem nowej wersji, będzie przechowywana w bazie danych zawartości. Po zainstalowaniu nowej karty należy przenieść tę zawartość na urządzenie StorSimple. `RBS Migrate()`Do migrowania zawartości można użyć polecenia cmdlet programu Microsoft PowerShell dołączonego do programu SharePoint. Aby uzyskać więcej informacji, zobacz [Migrowanie zawartości do lub z SPZ](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Aby uaktualnić adapter StorSimple dla programu SharePoint
1. Odinstaluj poprzednią wersję adaptera StorSimple dla programu SharePoint.
   
   > [!NOTE]
   > Spowoduje to automatyczne wyłączenie SPZ w bazach danych zawartości. Istniejące obiekty blob pozostaną jednak na urządzeniu StorSimple. Ponieważ struktura SPZ jest wyłączona, a obiekty blob nie zostały zmigrowane z powrotem do baz danych zawartości, żadne żądania tych obiektów BLOB zakończą się niepowodzeniem. 
   > 
   > 
2. Zainstaluj nową kartę StorSimple dla programu SharePoint. Nowy adapter automatycznie rozpoznaje bazy danych zawartości, które zostały wcześniej włączone lub wyłączone dla SPZ, i użyje poprzednich ustawień.
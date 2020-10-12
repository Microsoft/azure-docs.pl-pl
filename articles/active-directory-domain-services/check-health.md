---
title: Sprawdź kondycję Azure Active Directory Domain Services | Microsoft Docs
description: Dowiedz się, jak sprawdzić kondycję domeny zarządzanej Azure Active Directory Domain Services (Azure AD DS) i zrozumieć komunikaty o stanie przy użyciu Azure Portal.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 34f4ecfd4cc3432babbd26d65bd37ea4d1fb882c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86040422"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Sprawdzanie kondycji domeny zarządzanej Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) uruchamia niektóre zadania w tle, aby zachować kondycję i aktualność domeny zarządzanej. Te zadania obejmują tworzenie kopii zapasowych, stosowanie aktualizacji zabezpieczeń i synchronizowanie danych z usługi Azure AD. Jeśli wystąpią problemy z domeną zarządzaną platformy Azure AD DS, te zadania mogą nie zostać pomyślnie ukończone. Aby przejrzeć i rozwiązać wszelkie problemy, można sprawdzić stan kondycji domeny zarządzanej przy użyciu Azure Portal.

W tym artykule pokazano, jak wyświetlić stan kondycji usługi Azure AD DS i poznać wyświetlone informacje lub alerty.

## <a name="view-the-health-status"></a>Wyświetl stan kondycji

Stan kondycji dla domeny zarządzanej jest wyświetlany przy użyciu Azure Portal. Informacje o czasie ostatniej kopii zapasowej i synchronizacji z usługą Azure AD można zobaczyć wraz z alertami, które wskazują na problem z kondycją domeny zarządzanej. Aby wyświetlić stan kondycji domeny zarządzanej, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**.
1. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Po lewej stronie okna zasobów AD DS platformy Azure wybierz pozycję **kondycja**. Poniższy przykładowy zrzut ekranu pokazuje domenę zarządzaną w dobrej kondycji oraz stan ostatniej synchronizacji kopii zapasowej i usługi Azure AD:

    ![Przegląd strony kondycji w Azure Portal pokazywania stanu Azure Active Directory Domain Services](./media/check-health/health-page.png)

*Ostatnia Ocena* sygnatury czasowej strony kondycji jest wyświetlana, gdy została ostatnio sprawdzona domena zarządzana. Kondycja domeny zarządzanej jest oceniana co godzinę. Jeśli wprowadzisz jakiekolwiek zmiany w domenie zarządzanej, poczekaj na następny cykl oceny, aby wyświetlić zaktualizowany stan kondycji.

Stan w prawym górnym rogu wskazuje ogólną kondycję domeny zarządzanej. Stan wszystkich istniejących alertów w domenie. W poniższej tabeli przedstawiono szczegółowe informacje o dostępnych wskaźnikach stanu:

| Stan | Ikona | Objaśnienie |
| --- | :----: | --- |
| Uruchomienie | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Domena zarządzana działa prawidłowo i nie ma alertów krytycznych ani ostrzeżeń. Domena może mieć alerty informacyjne. |
| Wymaga uwagi (ostrzeżenie) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Brak alertów krytycznych w domenie zarządzanej, ale istnieje co najmniej jeden Alert ostrzegawczy, który powinien zostać rozkierowany. |
| Wymaga uwagi (krytyczne) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Istnieje co najmniej jeden alert krytyczny w domenie zarządzanej, do którego należy rozwiązać problem. Możesz również mieć alerty ostrzegawcze i/lub informacyjne. |
| Wdrażanie | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Trwa wdrażanie domeny zarządzanej. |

## <a name="understand-monitors-and-alerts"></a>Informacje o monitorach i alertach

Stan kondycji domeny zarządzanej przedstawia dwa typy *monitorów*informacji i *alertów*. Monitory przedstawiają czas ukończenia podstawowych zadań w tle. Alerty zawierają informacje lub sugestie dotyczące zwiększenia stabilności domeny zarządzanej.

### <a name="monitors"></a>Monitory

Monitory to obszary domeny zarządzanej, które są regularnie sprawdzane. Jeśli istnieją aktywne alerty dla domeny zarządzanej, może to spowodować zgłoszenie problemu przez jeden z monitorów. Usługa Azure AD DS aktualnie ma monitory dla następujących obszarów:

* Backup
* Synchronizacja z usługą Azure AD

#### <a name="backup-monitor"></a>Monitor kopii zapasowych

Monitor kopii zapasowej sprawdza, czy pomyślnie uruchomiono zautomatyzowane regularne kopie zapasowe domeny zarządzanej. W poniższej tabeli przedstawiono szczegółowe informacje o dostępnym stanie monitora kopii zapasowych:

| Wartość Szczegółowa | Objaśnienie |
| --- | --- |
| Nigdy nie wykonano kopii zapasowej | Ten stan jest normalny w przypadku nowych domen zarządzanych. Najpierw należy utworzyć kopię zapasową po 24 godzinach od wdrożenia domeny zarządzanej. Jeśli ten stan będzie się utrzymywał, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support]. |
| Ostatnia kopia zapasowa została wykonana od 1 do 14 dni temu | Ten zakres czasu jest oczekiwanym stanem monitora kopii zapasowych. W tym okresie zautomatyzowane regularne kopie zapasowe powinny być wykonywane. |
| Ostatnia kopia zapasowa została wykonana ponad 14 dni temu. | Przedział czasu dłuższy niż dwa tygodnie wskazuje na problem z automatycznymi regularnymi kopiami zapasowymi. Aktywne alerty krytyczne mogą uniemożliwiać utworzenie kopii zapasowej domeny zarządzanej. Rozwiąż wszystkie aktywne alerty dla domeny zarządzanej. Jeśli monitor kopii zapasowych nie aktualizuje stanu, aby zgłosić ostatnią kopię zapasową, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Synchronizacja z usługą Azure AD monitor

Domena zarządzana regularnie przeprowadza synchronizację z Azure Active Directory. Liczba użytkowników i obiektów grup oraz liczba zmian wprowadzonych w katalogu usługi Azure AD od momentu ostatniej synchronizacji wpływa na czas synchronizacji. Jeśli zarządzana domena była ostatnio synchronizowana przez trzy dni temu, sprawdź i Rozwiąż wszystkie aktywne alerty. Jeśli monitor synchronizacji nie aktualizuje stanu, aby wyświetlić ostatnią synchronizację po rozpoczęciu wszystkich aktywnych alertów, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support].

### <a name="alerts"></a>Alerty

Alerty są generowane w przypadku problemów w domenie zarządzanej, które należy rozwiązać, aby usługa działała poprawnie. Każdy alert wyjaśnia problem i zawiera adres URL, który przedstawia poszczególne kroki w celu rozwiązania problemu. Aby uzyskać więcej informacji na temat możliwych alertów i ich rozwiązań, zobacz [Rozwiązywanie problemów z alertami](troubleshoot-alerts.md).

Alerty stanu kondycji są podzielone na następujące poziomy ważności:

 * **Alerty krytyczne** to problemy, które poważnie wpływają na domenę zarządzaną. Te alerty powinny być natychmiast rozwiązywane. Platforma Azure nie może monitorować, zmieniać i synchronizować domeny zarządzanej, dopóki problemy nie zostaną rozwiązane.
 * **Alerty ostrzegawcze** powiadamiają o problemach, które mogą mieć wpływ na operacje w domenie zarządzanej, jeśli problem będzie nadal występował. Te alerty oferują również zalecenia dotyczące zabezpieczania domeny zarządzanej.
 * **Alerty informacyjne** to powiadomienia, które nie mają negatywnego wpływu na domenę zarządzaną. Alerty informacyjne zawierają informacje o tym, co się dzieje w domenie zarządzanej.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat alertów, które są wyświetlane na stronie stan kondycji, zobacz temat [Rozwiązywanie alertów w domenie zarządzanej][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md

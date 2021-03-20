---
title: Odnawianie wygasłego właściciela grupy przypisań członków w Privileged Identity Management — Azure AD | Microsoft Docs
description: Dowiedz się, jak przedłużyć lub odnowić przypisania grup przypisane do ról w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87505993"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Przedłuż lub Odnów przypisania uprzywilejowanych grup dostępu (wersja zapoznawcza) w Privileged Identity Management

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) oferuje kontrolki do zarządzania cyklem życia dostępu i przypisania dla uprzywilejowanych grup dostępu. Administratorzy mogą przypisywać role przy użyciu właściwości daty i godziny rozpoczęcia i zakończenia. Gdy zbliża się zakończenie przypisania, Privileged Identity Management wysyła powiadomienia e-mail do odpowiednich użytkowników lub grup. Wysyła również powiadomienia e-mail do administratorów zasobu, aby zapewnić zachowanie odpowiedniego dostępu. Przypisania mogą zostać odnowione i pozostają widoczne w stanie wygaśnięcia przez maksymalnie 30 dni, nawet jeśli nie przedłużono dostępu.

## <a name="who-can-extend-and-renew"></a>Kto może przedłużyć i odnowić

Tylko Administratorzy zasobu mogą przedłużyć lub odnowić przypisania uprzywilejowanych grup dostępu. Użytkownik lub Grupa, której to dotyczy, może zażądać przedłużenia przypisań, które wkrótce wygasną, i zażądać odnowienia już wygasłych przypisań.

## <a name="when-notifications-are-sent"></a>Po wysłaniu powiadomień

Privileged Identity Management wysyła powiadomienia e-mail do administratorów i użytkowników, którzy mają naruszone przypisania uprzywilejowanych grup dostępu, które wygasną:

- W ciągu 14 dni przed wygaśnięciem
- Jeden dzień przed wygaśnięciem
- Po wygaśnięciu przypisania

Administratorzy odbierają powiadomienia, gdy użytkownik lub Grupa żąda przedłużenia lub odnowienia przypisania lub wygaśnięcia ważności. Gdy administrator rozwiąże żądanie, wszyscy administratorzy i użytkownik żądający są powiadamiani o zatwierdzeniu lub odmowie.

## <a name="extend-group-assignments"></a>Rozwiń przydziały grupy

Poniższe kroki przedstawiają proces żądania, rozwiązywania lub administrowania rozszerzeniem lub odnowieniem przypisania grupy.

### <a name="self-extend-expiring-assignments"></a>Przypisania wygasania samoobsługowego

Użytkownicy przypisani do uprzywilejowanej grupy dostępu mogą rozszerać przypisania grup wygasające bezpośrednio z karty **uprawniony** lub **aktywny** na stronie **przypisania** dla grupy. Użytkownicy lub grupy mogą zażądać rozbudowania kwalifikujących się i aktywnych przydziałów, których ważność wygaśnie w ciągu następnych 14 dni.

![Strona Moje role wyświetla listę uprawniających assgnments z kolumną Action](media/groups-renew-extend/self-extend-group-assignment.png)

Gdy data/godzina zakończenia przydziału przypada w ciągu 14 dni, jest dostępne polecenie **rozszerzające** . Aby zażądać rozszerzenia przypisania grupy, wybierz pozycję **rozszerzanie** , aby otworzyć formularz żądania.

![Rozwiń okienko przypisania grupy z powodu pola przyczyny i szczegółów](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>Zalecamy uwzględnienie szczegółowych informacji o tym, dlaczego rozszerzenie jest niezbędne, oraz o tym, jak długo powinno zostać przyznane rozszerzenie (Jeśli masz te informacje).

W danej chwili Administratorzy otrzymają powiadomienie e-mail z prośbą o przejrzenie żądania rozszerzenia. Jeśli żądanie przekroczenia zostało już przesłane, w portalu pojawi się powiadomienie platformy Azure.

Aby wyświetlić stan lub anulować żądanie, Otwórz stronę **oczekujące żądania** dla przypisania grupy.

![Przydziały oczekujących grup dostępu uprzywilejowanego — żądanie dotyczące anulowania](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Rozszerzenie zatwierdzone przez administratora

Gdy użytkownik lub Grupa przesyła żądanie, aby zwiększyć przypisanie grupy, Administratorzy otrzymają powiadomienie e-mail zawierające szczegóły oryginalnego przypisania i przyczynę żądania. Powiadomienie zawiera bezpośredni link do żądania zatwierdzenia lub odrzucenia przez administratora.

Oprócz korzystania z linku pocztą e-mail Administratorzy mogą zatwierdzać lub odrzucać żądania, przechodząc do portalu administracyjnego Privileged Identity Management i wybierając pozycję **Zatwierdź żądania** w okienku po lewej stronie.

![Przydziały uprzywilejowanych grup dostępu — zatwierdzanie żądań na stronie żądań i linków do zatwierdzenia lub odmowy](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

Gdy administrator wybierze pozycję **Zatwierdź** lub **Odmów**, szczegóły żądania są wyświetlane wraz z polem, aby zapewnić firmie uzasadnienie dla dzienników inspekcji.

![Zatwierdź żądanie przypisania grupy z przyczyną żądającą, typem przypisania, czasem rozpoczęcia, czasem zakończenia i przyczynie](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

Podczas zatwierdzania żądania rozszerzającego przypisanie grupy Administratorzy zasobów mogą wybrać nową datę początkową, datę końcową i typ przypisania. Zmiana typu przypisania może być konieczna, jeśli administrator chce udzielić ograniczonego dostępu do wykonania określonego zadania (na przykład jeden dzień). W tym przykładzie administrator może zmienić przypisanie z **kwalifikujące** się do **aktywne**. Oznacza to, że mogą oni zapewnić dostęp do osoby żądającej bez konieczności uaktywniania jej.

### <a name="admin-initiated-extension"></a>Rozszerzenie zainicjowane przez administratora

Jeśli użytkownik przypisany do grupy nie zażąda rozszerzenia dla przypisania grupy, administrator może przedłużyć przypisanie w imieniu użytkownika. Rozszerzenia administracyjne przypisywania grup nie wymagają zatwierdzenia, ale powiadomienia są wysyłane do wszystkich innych administratorów po przedłużoniu przydziału.

Aby zwiększyć przypisanie grupy, przejdź do widoku przypisanie w Privileged Identity Management. Znajdź przypisanie, które wymaga rozszerzenia. Następnie wybierz pozycję **Rozciągnij** w kolumnie Akcja.

![Strona przydziałów z listą kwalifikujących się przypisań grup z linkami do](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>Odnów przypisania grupy

Chociaż koncepcyjnie przypomina proces żądania rozszerzenia, proces odnawiania wygasłego przypisania grupy jest inny. Wykonując następujące kroki, przypisania i Administratorzy mogą odnowić dostęp do wygasłych przydziałów w razie potrzeby.

### <a name="self-renew"></a>Samodzielne odnawianie

Użytkownicy, którzy nie będą mieli już dostępu do zasobów, mogą uzyskać dostęp do 30-dniowej historii przydziału. W tym celu przejdź do **obszaru Moje role** w lewym okienku, a następnie wybierz kartę **wygasłe przydziały** .

![Strona Moje role — wygasłe karty przypisania](media/groups-renew-extend/groups-renew-from-my-roles.png)

Lista przypisań wyświetla wartości domyślne dla **kwalifikujących się przypisań**. Użyj menu rozwijanego, aby przełączać się między uprawnionymi a aktywnymi przypisaniami.

Aby zażądać odnowienia dla wszystkich przypisań grup na liście, wybierz akcję **Odnów** . Następnie podaj przyczynę żądania. Warto podać czas trwania poza wszelkimi dodatkowymi kontekstami lub uzasadnieniem biznesowym, które mogą pomóc administratorowi zasobów zdecydować o zatwierdzeniu lub odrzuceniu.

![Okno z przyczyną odnawiania grupy](media/groups-renew-extend/groups-renew-request-form.png)

Po przesłaniu żądania Administratorzy zasobów są powiadamiani o oczekującym żądaniu odnowienia przypisania grupy.

### <a name="admin-approves"></a>Administrator zatwierdza

Administratorzy zasobów mogą uzyskać dostęp do żądania odnowienia z linku w wiadomości e-mail lub przez uzyskanie dostępu do Privileged Identity Management z Azure Portal i wybranie opcji **Zatwierdź żądania** w okienku po lewej stronie.

Gdy administrator wybierze pozycję **Zatwierdź** lub **Odmów**, szczegóły żądania są wyświetlane wraz z polem, aby zapewnić firmie uzasadnienie dla dzienników inspekcji.

Podczas zatwierdzania żądania odnowienia przypisania grupy Administratorzy zasobów muszą wprowadzić nową datę początkową, datę końcową i typ przypisania.

## <a name="next-steps"></a>Następne kroki

- [Zatwierdzanie lub odrzucanie żądań dla przypisań uprzywilejowanych grup dostępu w Privileged Identity Management](groups-approval-workflow.md)
- [Skonfiguruj ustawienia grupy dostępu uprzywilejowanego w Privileged Identity Management](groups-role-settings.md)

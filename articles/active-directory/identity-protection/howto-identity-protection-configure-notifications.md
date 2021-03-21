---
title: Powiadomienia Azure Active Directory Identity Protection
description: Dowiedz się, jak powiadomienia obsługują Twoje działania dochodzeniowe.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9536cf41add73f494bfff451c201d36e951864e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997668"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Powiadomienia Azure Active Directory Identity Protection

Azure AD Identity Protection wysyła dwa typy zautomatyzowanych wiadomości e-mail z powiadomieniami, które ułatwiają zarządzanie ryzykiem użytkownika i wykrywaniem ryzyka:

- Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail
- Tygodniowy adres e-mail w postaci skróconej

Ten artykuł zawiera omówienie obu wiadomości e-mail z powiadomieniami.

## <a name="users-at-risk-detected-email"></a>Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail

W odpowiedzi na wykryte konto zagrożone Azure AD Identity Protection generuje alert e-mail z **użytkownikami narażonymi na ryzyko wykryte** jako temat. Wiadomość e-mail zawiera link do raportu **[Użytkownicy oflagowani w związku z ryzykiem](./overview-identity-protection.md)** . Najlepszym rozwiązaniem jest natychmiastowe badanie narażonych użytkowników.

Konfiguracja tego alertu umożliwia określenie poziomu ryzyka użytkownika, który ma zostać wygenerowany przez alert. Wiadomość e-mail zostanie wygenerowana, gdy poziom ryzyka użytkownika osiągnie określone elementy. Jeśli na przykład ustawisz zasady, aby otrzymywać alerty dotyczące średniego ryzyka użytkownika, a Ocena ryzyka użytkownika przez użytkownika Jan przejdzie na średnie ryzyko z powodu ryzyka związanego z logowaniem w czasie rzeczywistym, otrzymasz wiadomość e-mail z wykrytym ryzykiem. Jeśli użytkownik ma kolejne wykrycia ryzyka, które spowodują, że Obliczanie poziomu ryzyka użytkownika będzie określonym poziomem ryzyka (lub wyższym), otrzymasz komunikat o zagrożeniu wykrytym przez użytkownika w przypadku ponownego obliczenia wyniku ryzyka dla użytkowników. Na przykład jeśli użytkownik przejdzie do średniego ryzyka 1 stycznia, otrzymasz powiadomienie e-mail, jeśli ustawienia są ustawione na alert dotyczący średniego ryzyka. Jeśli ten sam użytkownik wykryje inne ryzyko wykrycia 5 stycznia, które również stanowi średniego ryzyka, a Ocena ryzyka użytkownika zostanie przeliczona i będzie nadal średnia, otrzymasz kolejną wiadomość e-mail z powiadomieniem. 

Jednak dodatkowe powiadomienie e-mail będzie wysyłane tylko wtedy, gdy wystąpiło wykrycie ryzyka (co spowodowało zmianę poziomu ryzyka użytkownika). Na przykład użytkownik loguje się 1 stycznia w dniu 5 AM i nie ma ryzyka w czasie rzeczywistym (oznacza to, że nie będzie można wygenerować poczty e-mail z powodu tego logowania). Dziesięć minut później, o godzinie 5:10, to ten sam użytkownik zostanie zalogowany i ma wysokie ryzyko w czasie rzeczywistym, co spowoduje, że poziom ryzyka użytkownika zostanie przeniesiony na wysoki i wiadomość e-mail do wysłania. Następnie, o godzinie 5:15, wynik ryzyka w trybie offline dla oryginalnego logowania w 5 AM zmieni się na wysokie ryzyko związane z przetwarzaniem ryzyka w trybie offline. Dodatkowy użytkownik oflagowany do wiadomości e-mail nie zostanie wysłany, ponieważ czas pierwszego logowania był przed drugim logowaniem, które już wyzwoliło powiadomienie e-mail.

Aby zapobiec przeciążeniu poczty e-mail, otrzymasz tylko jedną wiadomość e-mail w ciągu 5-sekundowego okresu. To opóźnienie oznacza, że jeśli wielu użytkowników przejdzie do określonego poziomu ryzyka w tym samym 5-sekundowym przedziale czasu, firma Microsoft agreguje i wyśle wiadomość e-mail, aby reprezentować zmianę poziomu ryzyka dla wszystkich z nich.

Jeśli Twoja organizacja włączyła samokorygowanie zgodnie z opisem w artykule, [środowisko użytkownika z Azure AD Identity Protection](concept-identity-protection-user-experience.md) istnieje szansa, że użytkownik może skorygować ryzyko przed rozpoczęciem badania. Można zobaczyć ryzykownych użytkowników i ryzykowne logowania, które zostały skorygowane przez dodanie "skorygowane" do filtru **stanu ryzyka** w raportach **ryzykownych użytkowników** lub **ryzykownych** logowań.

![Użytkownicy z wykrytymi zagrożeniami wiadomości e-mail](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Konfigurowanie alertów wykrytych dla użytkowników

Jako administrator możesz ustawić:

- **Poziom ryzyka użytkownika wyzwalający generowanie tej wiadomości e-mail** — domyślnie poziom ryzyka jest ustawiany na "wysoki".
- **Adresaci tej poczty e-mail** w rolach Administrator globalny, administrator zabezpieczeń lub czytelnik zabezpieczeń zostaną automatycznie dodani do tej listy. Podjęto próbę wysłania wiadomości e-mail do pierwszych 20 członków każdej roli. Jeśli użytkownik jest zarejestrowany w usłudze PIM w celu podniesienia uprawnień do jednej z tych ról na żądanie, **będzie otrzymywać tylko wiadomości e-mail w przypadku ich podniesienia uprawnień w momencie wysłania wiadomości e-mail**.
   - Opcjonalnie możesz **dodać niestandardowe wiadomości e-mail** , gdy zdefiniowane użytkownicy muszą mieć odpowiednie uprawnienia do wyświetlania raportów połączonych w Azure Portal.

Należy skonfigurować użytkowników pod kątem ryzykownych wiadomości e-mail w **Azure Portal** w obszarze **Azure Active Directory**  >    >  Użytkownicy **ochrony tożsamości** zabezpieczeń  >  **na wykryciu zagrożeń**.

## <a name="weekly-digest-email"></a>Tygodniowy adres e-mail w postaci skróconej

Cotygodniowa wiadomość e-mail z podsumowaniem zawiera streszczenie nowych wykrytych zagrożeń.  
Obejmuje:

- Wykryto nowych ryzykownych użytkowników
- Wykryto nowe ryzykowne logowania (w czasie rzeczywistym)
- Linki do pokrewnych raportów w usłudze Identity Protection

![Tygodniowy adres e-mail w postaci skróconej](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Użytkownicy z rolami administratora globalnego, administratora zabezpieczeń lub czytelnika zabezpieczeń są automatycznie dodawani do tej listy. Podjęto próbę wysłania wiadomości e-mail do pierwszych 20 członków każdej roli. Jeśli użytkownik jest zarejestrowany w usłudze PIM, aby podwyższyć poziom jednej z tych ról na żądanie, **otrzymają oni tylko te wiadomości e-mail, jeśli zostaną one podniesione w chwili wysłania wiadomości e-mail** .

### <a name="configure-weekly-digest-email"></a>Konfigurowanie tygodniowego adresu e-mail w postaci skróconej

Jako administrator możesz przełączać wysyłanie cotygodniowej wiadomości e-mail z podsumowaniem lub wyłączyć ją i wybrać użytkowników przypisanych do odbierania wiadomości e-mail.

Skonfiguruj cotygodniowe wiadomości e-mail z podsumowaniem w **Azure Portal** w obszarze **Azure Active Directory**  >  **Security**  >  **Identity Protection**  >  .

## <a name="see-also"></a>Zobacz też

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)

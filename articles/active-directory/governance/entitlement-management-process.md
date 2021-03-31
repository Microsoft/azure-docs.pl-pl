---
title: Żądanie & powiadomień o procesie — Zarządzanie prawami w usłudze Azure AD
description: Dowiedz się więcej o procesie żądania dla pakietu dostępu i powiadomieniach e-mail, które są wysyłane w Azure Active Directory zarządzania prawami.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb84eae2db01180865db54250598f45c7b2ddca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746239"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Zażądaj procesu i powiadomień e-mail w usłudze Azure AD uprawnienia do zarządzania

Gdy użytkownik przesyła żądanie do pakietu dostępu, rozpocznie się dostarczenie tego żądania dostępu. Zarządzanie prawami w usłudze Azure AD wysyła powiadomienia e-mail do osób zatwierdzających i osoby żądające w przypadku wystąpienia kluczowych zdarzeń w trakcie procesu. W tym artykule opisano proces żądania i wysyłane powiadomienia e-mail.

## <a name="request-process"></a>Proces żądania

Użytkownik wymagający dostępu do pakietu dostępu może przesłać żądanie dostępu. W zależności od konfiguracji zasad żądanie może wymagać zatwierdzenia. Gdy żądanie zostanie zatwierdzone, proces zacznie przypisywać użytkownikowi dostęp do każdego zasobu w pakiecie dostępu. Na poniższym diagramie przedstawiono przegląd procesu i różne stany:

![Diagram procesu zatwierdzania](./media/entitlement-management-process/request-process.png)

| Stan | Opis |
| --- | --- |
| Przesłane | Użytkownik przesyła żądanie. |
| Oczekiwanie na zatwierdzenie | Jeśli zasady dla pakietu dostępu wymagają zatwierdzenia, żądanie przechodzi do oczekującego zatwierdzenia. |
| Wygasłe | Jeśli żadne osoby zatwierdzające nie zatwierdzą żądania w ramach limitu czasu żądania zatwierdzenia, żądanie wygasa. Aby ponowić próbę, użytkownik będzie musiał ponownie przesłać żądanie. |
| Odmowa | Osoba zatwierdzająca odrzuca żądanie. |
| Approved (Zatwierdzono) | Osoba zatwierdzająca zatwierdza żądanie. |
| Wykonania | Użytkownikowi **nie** przypisano dostępu do wszystkich zasobów w pakiecie dostępu. Jeśli jest to użytkownik zewnętrzny, użytkownik może nie mieć jeszcze dostępu do katalogu zasobów. Ponadto nie zaakceptowali monitu o zgodę. |
| Dostarczono | Użytkownikowi przypisano dostęp do wszystkich zasobów w pakiecie dostępu. |
| Dostęp rozszerzony | Jeśli w zasadach są dozwolone rozszerzenia, Użytkownik rozszerzył przypisanie. |
| Dostęp wygasł | Dostęp użytkownika do pakietu dostępu wygasł. Aby uzyskać dostęp ponownie, użytkownik będzie musiał przesłać żądanie. |

## <a name="email-notifications"></a>Powiadomienia e-mail

Jeśli jesteś osobą zatwierdzającą, wysyłasz powiadomienia pocztą e-mail, gdy musisz zatwierdzić żądanie dostępu. Otrzymasz również powiadomienia, gdy żądanie dostępu zostało zakończone. Wysyłane są również powiadomienia e-mail, które wskazują stan Twojego żądania, jeśli jesteś zleceniodawcą.

Na poniższych diagramach przedstawiono, kiedy te powiadomienia e-mail są wysyłane do osób zatwierdzających lub osoby żądającej. Odwołuje się do [tabeli powiadomień e-mail](entitlement-management-process.md#email-notifications-table) , aby znaleźć odpowiednią liczbę powiadomień e-mail wyświetlanych na diagramach.

### <a name="first-approvers-and-alternate-approvers"></a>Pierwsze osoby zatwierdzające i alternatywni osoby zatwierdzające
Na poniższym diagramie przedstawiono środowisko pierwszej osoby zatwierdzającej i alternatywne osoby zatwierdzające oraz powiadomienia e-mail otrzymywane podczas procesu żądania:

![Pierwszy i alternatywny przepływ procesu zatwierdzania](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Osoby żądające
Na poniższym diagramie przedstawiono środowisko żądających i powiadomienia e-mail otrzymywane podczas procesu żądania:

![Przepływ procesu żądającego](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>2 — zatwierdzenie na etapie
Na poniższym diagramie przedstawiono środowisko osób zatwierdzających etap-1 i (etap 2) oraz powiadomienia e-mail otrzymywane podczas procesu żądania:

![2 — przepływ procesu zatwierdzania na etapie](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabela powiadomień e-mail
Poniższa tabela zawiera więcej szczegółów na temat każdej z tych powiadomień e-mail. Aby zarządzać tymi wiadomościami e-mail, możesz użyć reguł. Na przykład w programie Outlook można utworzyć reguły przenoszenia wiadomości e-mail do folderu, jeśli temat zawiera wyrazy z tej tabeli:

| # | Temat wiadomości e-mail | Po wysłaniu | Wysłane do |
| --- | --- | --- | --- |
| 1 | Wymagana akcja: Zatwierdź lub Odrzuć przekazane żądanie przez *[Date]* | Ta wiadomość e-mail będzie wysyłana do osoby zatwierdzającej alternatywny etap 1 (po eskalacji żądania) w celu podjęcia odpowiednich działań. | Etap-1 alternatywne osoby zatwierdzające |
| 2 | Wymagana akcja: Zatwierdź lub Odmów żądania przez *[Date]* | Ta wiadomość e-mail zostanie wysłana do pierwszej osoby zatwierdzającej, jeśli eskalacja jest wyłączona, aby podjąć odpowiednie działania. | Pierwsza osoba zatwierdzająca |
| 3 | Przypomnienie: Zatwierdź lub Odrzuć żądanie przez *[Date]* dla *[osoba żądająca]* | Ta wiadomość e-mail z przypomnieniem zostanie wysłana do pierwszej osoby zatwierdzającej, jeśli eskalacja jest wyłączona. Wiadomość e-mail prosi o podjęcie działania, jeśli nie. | Pierwsza osoba zatwierdzająca |
| 4 | Zatwierdź lub Odrzuć żądanie przez *[Time]* w dniu *[Date]* | Ta wiadomość e-mail zostanie wysłana do pierwszej osoby zatwierdzającej (w przypadku włączenia eskalacji) w celu podjęcia odpowiednich działań. | Pierwsza osoba zatwierdzająca |
| 5 | Przypomnienie o wymaganym akcji: Zatwierdź lub Odrzuć żądanie przez *[Date]* dla *[żądającego]* | Ta wiadomość e-mail z przypomnieniem zostanie wysłana do pierwszej osoby zatwierdzającej, jeśli eskalacja jest włączona. Wiadomość e-mail prosi o podjęcie działania, jeśli nie. | Pierwsza osoba zatwierdzająca |
| 6 | Żądanie wygasło dla *[access_package]* | Ta wiadomość e-mail zostanie wysłana do pierwszej osoby zatwierdzającej i od 1 alternatywnej osoby zatwierdzające od momentu wygaśnięcia żądania. | Pierwsze osoby zatwierdzającej, etap 1. alternatywne osoby zatwierdzające |
| 7 | Żądanie zatwierdzone dla *[żądającej]* do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do pierwszej osoby zatwierdzającej i od 1 alternatywnej osoby zatwierdzające do momentu ukończenia żądania. | Pierwsze osoby zatwierdzającej, etap 1. alternatywne osoby zatwierdzające |
| 8 | Żądanie zatwierdzone dla *[żądającej]* do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do pierwszej osoby zatwierdzającej i od 1 do zaakceptowania alternatywne osoby zatwierdzające 2-etapowe żądania po zatwierdzeniu żądania etapu 1. | Pierwsze osoby zatwierdzającej, etap 1. alternatywne osoby zatwierdzające |
| 9 | Żądanie odrzucone do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do osoby żądającej, gdy żądanie zostanie odrzucone | Requestor |
| 10 | Twoje żądanie wygasło dla *[access_package]* | Ta wiadomość e-mail zostanie wysłana do osoby żądającej na końcu pojedynczego lub dwuetapowego żądania. Wiadomość e-mail powiadamia żądającego o wygaśnięciu żądania. | Requestor |
| 11 | Wymagana akcja: Zatwierdź lub Odmów żądania przez *[Date]* | Ta wiadomość e-mail zostanie wysłana do drugiej osoby zatwierdzającej, jeśli eskalacja jest wyłączona, aby podjąć odpowiednie działania. | Druga osoba zatwierdzająca |
| 12 | Przypomnienie o wymaganym akcji: Zatwierdź lub Odrzuć żądanie przez *[Date]* | Ta wiadomość e-mail z przypomnieniem zostanie wysłana do drugiej osoby zatwierdzającej, jeśli eskalacja jest wyłączona. Powiadomienie poprosi o podjęcie działania, jeśli jeszcze nie zostało to zrobione. | Druga osoba zatwierdzająca |
| 13 | Wymagana akcja: Zatwierdź lub Odrzuć żądanie przez *[Date]* dla *[żądającego]* | Ta wiadomość e-mail zostanie wysłana do drugiej osoby zatwierdzającej, jeśli eskalacja jest włączona, aby podjąć działanie. | Druga osoba zatwierdzająca |
| 14 | Przypomnienie o wymaganym akcji: Zatwierdź lub Odrzuć żądanie przez *[Date]* dla *[żądającego]* | Ta wiadomość e-mail z przypomnieniem zostanie wysłana do drugiej osoby zatwierdzającej, jeśli eskalacja jest włączona. Powiadomienie poprosi o podjęcie działania, jeśli jeszcze nie zostało to zrobione. | Druga osoba zatwierdzająca |
| 15 | Wymagana akcja: Zatwierdź lub Odrzuć przekazane żądanie przez *[Date]* | Ta wiadomość e-mail zostanie wysłana do osoby zatwierdzającej alternatywnego etapu 2, jeśli eskalacja jest włączona, aby podjąć działania. | Etapy — 2 alternatywne osoby zatwierdzające |
| 16 | Żądanie zatwierdzone dla *[żądającej]* do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do drugiej osoby zatwierdzającej i przełożonej na 2 zastępców osoby zatwierdzające po zatwierdzeniu żądania. | Druga osoba zatwierdzająca, etap-2 alternatywne osoby zatwierdzające |
| 17 | Żądanie wygasło dla *[access_package]* | Ta wiadomość e-mail zostanie wysłana do drugiej osoby zatwierdzającej lub alternatywnej osoby zatwierdzającej po wygaśnięciu żądania. | Druga osoba zatwierdzająca, etap-2 alternatywne osoby zatwierdzające |
| 18 | Masz teraz dostęp do programu *[access_package]* | Ta wiadomość e-mail zostanie wysłana do użytkowników końcowych, aby rozpocząć korzystanie z nich. | Requestor |
| 19 | Rozszerzona dostęp dla *[access_package]* przez *[Date]* | Ta wiadomość e-mail zostanie wysłana do użytkowników końcowych przed wygaśnięciem dostępu. | Requestor |
| 20 | Zakończono dostęp dla *[access_package]* | Ta wiadomość e-mail zostanie wysłana do użytkowników końcowych po wygaśnięciu dostępu. | Requestor |

### <a name="access-request-emails"></a>Adresy e-mail żądania dostępu

Gdy obiekt żądający przesyła żądanie dostępu do pakietu dostępu skonfigurowanego do żądania zatwierdzenia, wszystkie osoby zatwierdzające dodani do zasad otrzymają powiadomienie e-mail z informacjami dotyczącymi żądania. Szczegóły zawarte w wiadomościach e-mail obejmują: organizacja osoby żądającej i uzasadnienie biznesowe. i żądana Data rozpoczęcia i zakończenia dostępu (jeśli została podana). Szczegóły obejmują również czas przesyłania żądania oraz czas wygaśnięcia żądania.

Wiadomość e-mail zawiera osoby zatwierdzające łącza, które można kliknąć, aby przejść do obszaru mój dostęp, aby zatwierdzić lub odrzucić żądanie dostępu. Oto przykładowe powiadomienie e-mail wysyłane do pierwszej osoby zatwierdzającej lub drugiej osoby zatwierdzającej (Jeśli włączono zatwierdzanie 2-etapowe) w celu ukończenia żądania dostępu:

![Zatwierdź żądanie uzyskania dostępu do poczty e-mail pakietu](./media/entitlement-management-shared/approver-request-email.png)

Osoby zatwierdzające mogą również otrzymywać wiadomości e-mail z przypomnieniem. Wiadomość e-mail prosi osoby zatwierdzającej o podejmowanie decyzji. Poniżej znajduje się przykładowe powiadomienie e-mail, które osoba zatwierdzająca otrzymuje w celu poinformowania o konieczności podjęcia działania:

![Adres e-mail żądania dostępu do przypomnienia](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Alternatywne osoby zatwierdzające wiadomości e-mail

Jeśli ustawienie alternatywne osoby zatwierdzające jest włączone, a żądanie nadal oczekuje, zostanie przesłane dalej. Alternatywne osoby zatwierdzające otrzymają wiadomość e-mail w celu zatwierdzenia lub odrzucenia żądania. Alternatywne osoby zatwierdzające można włączyć w obszarze etap-1 i etap 2. Oto przykładowa wiadomość e-mail z powiadomieniem, że osoby zatwierdzające alternatywne otrzymują:

![Adresy e-mail żądania alternatywnej osoby zatwierdzające](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Osoby zatwierdzające i alternatywne osoby zatwierdzające mogą zatwierdzić lub odrzucić żądanie.

### <a name="approved-or-denied-emails"></a>Zatwierdzone lub odrzucone wiadomości e-mail

 Gdy osoba zatwierdzająca odbiera żądanie dostępu przesłane przez żądającego, może zatwierdzić lub odrzucić żądanie dostępu. Osoba zatwierdzająca musi dodać uzasadnienie biznesowe dla swojej decyzji. Oto przykładowa wiadomość e-mail wysyłana do osób zatwierdzających i zastępców osób zatwierdzających po zatwierdzeniu żądania:

![Zatwierdzone żądanie dostępu do pakietu do poczty e-mail](./media/entitlement-management-process/approver-request-email-approved.png)

Gdy żądanie dostępu zostanie zatwierdzone, a dostęp do nich jest inicjowany, do osoby żądającej jest wysyłana wiadomość e-mail z informacją o tym, że mają teraz dostęp do pakietu dostępu. Oto przykładowe powiadomienie e-mail wysyłane do osoby żądającej, gdy zostanie udzielony dostęp do pakietu dostępu:

![Zatwierdzony adres e-mail żądania dostępu do żądającego](./media/entitlement-management-process/requestor-email-approved.png)

W przypadku odrzucenia żądania dostępu do osoby żądającej jest wysyłana wiadomość e-mail z powiadomieniem. Oto przykładowe powiadomienie e-mail wysyłane do osoby żądającej, gdy żądanie dostępu zostanie odrzucone:

![Adres e-mail żądania żądania osoby żądającej](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>2 — adresy e-mail żądań dostępu do zatwierdzenia

Jeśli jest włączone zatwierdzanie 2-etapowe, co najmniej dwie osoby zatwierdzające muszą zatwierdzić żądanie, jedno z poszczególnych etapów, zanim osoba żądająca będzie mogła uzyskać dostęp.

W trakcie etapu 1 pierwsza osoba zatwierdzająca otrzyma wiadomość e-mail z żądaniem dostępu i podejmie decyzję. W przypadku zatwierdzenia żądania wszystkie pierwsze osoby zatwierdzające i alternatywne osoby zatwierdzające w etapie-1 (jeśli eskalacji są włączone) otrzymają powiadomienie o ukończeniu etapu 1. Oto przykładowa wiadomość e-mail z powiadomieniem wysyłanym po zakończeniu etapu-1:

![2 — adres e-mail żądania dostępu](./media/entitlement-management-process/approver-request-email-2stage.png)

Po zatwierdzeniu przez pierwszej lub alternatywnej osoby zatwierdzającej żądanie w etapie-1 rozpoczyna się etap 2. W trakcie etapu 2 druga osoba zatwierdzająca otrzyma wiadomość e-mail z powiadomieniem o żądaniu dostępu. Po drugiej osobie zatwierdzającej lub alternatywne osoby zatwierdzające w fazie 2 (Jeśli włączono eskalację) zdecydują się na zatwierdzenie lub odrzucenie żądania, wiadomości e-mail z powiadomieniem są wysyłane do pierwszej i drugiej osoby zatwierdzającej oraz do wszystkich osób zatwierdzających w ramach etapu-1 i etapu-2, a także od osoby żądającej.

### <a name="expired-access-request-emails"></a>Wygasłe wiadomości e-mail z żądaniem dostępu

Żądania dostępu mogły wygasnąć, jeśli żadna osoba zatwierdzająca nie zatwierdziła lub nie odrzuciła żądania. 

Gdy żądanie osiągnie swoją skonfigurowaną datę wygaśnięcia i wygasa, nie może być już zatwierdzone ani odrzucone przez osoby zatwierdzające. Oto przykładowa wiadomość e-mail z powiadomieniem wysyłanym do wszystkich pierwszych, drugi (Jeśli włączono zatwierdzanie 2-etapowe) i zastępców osób zatwierdzających:

![Osoby zatwierdzające utraciły wiadomość e-mail z żądaniem dostępu](./media/entitlement-management-process/approver-request-email-expired.png)

Powiadomienie e-mail jest również wysyłane do osoby żądającej, powiadamiają o tym, że ich żądanie dostępu wygasło, i że muszą ponownie przesłać żądanie dostępu. Na poniższym diagramie przedstawiono środowisko osoby żądającej i powiadomienia e-mail odbierane po zażądaniu przekroczenia dostępu:

![Przepływ procesu rozszerzonego dostępu żądania](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Oto przykładowe powiadomienie e-mail wysyłane do osoby żądającej, gdy ich żądanie dostępu wygasło:

![Wiadomość e-mail z żądaniem żądania dostępu wygasła](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Następne kroki

- [Zażądaj dostępu do pakietu dostępu](entitlement-management-request-access.md)
- [Zatwierdzanie lub odrzucanie żądań dostępu](entitlement-management-request-approve.md)

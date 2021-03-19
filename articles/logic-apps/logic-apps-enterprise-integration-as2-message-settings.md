---
title: Ustawienia komunikatów AS2
description: Przewodnik referencyjny dotyczący ustawień wysyłania i odbierania AS2 w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74793025"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dokumentacja dla ustawień komunikatu AS2 w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw

Ta dokumentacja zawiera opis właściwości, które można ustawić w celu określenia sposobu, w jaki umowa AS2 obsługuje komunikaty wysyłane i odbierane przez partnerów handlowych. Skonfiguruj te właściwości na podstawie umowy z partnerem, który wymienia z nim wiadomości.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 Ustawienia odbierania

![Wybieranie opcji "Odbierz ustawienia"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| **Zastąp właściwości komunikatu** | Nie | Przesłania właściwości przychodzących komunikatów przy użyciu ustawień właściwości. |
| **Komunikat powinien być podpisany** | Nie | Określa, czy wszystkie wiadomości przychodzące muszą być podpisane cyfrowo. Jeśli wymagasz podpisywania, z listy **certyfikat** wybierz istniejący certyfikat publiczny partnera gościa, aby sprawdzić poprawność podpisu w komunikatach. Jeśli nie masz certyfikatu, Dowiedz się więcej o [dodawaniu certyfikatów](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Komunikat powinien być zaszyfrowany** | Nie | Określa, czy wszystkie wiadomości przychodzące muszą być szyfrowane. Nieszyfrowane wiadomości są odrzucane. Jeśli wymagane jest szyfrowanie, z listy **certyfikat** wybierz istniejący certyfikat prywatny partnera hosta na potrzeby odszyfrowywania wiadomości przychodzących. Jeśli nie masz certyfikatu, Dowiedz się więcej o [dodawaniu certyfikatów](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Komunikat powinien być skompresowany** | Nie | Określa, czy wszystkie komunikaty przychodzące muszą być kompresowane. Nieskompresowane komunikaty są odrzucane. |
| **Nie Zezwalaj na duplikaty identyfikatora wiadomości** | Nie | Określa, czy zezwalać na komunikaty ze zduplikowanymi identyfikatorami. W przypadku niezezwalania na zduplikowane identyfikatory wybierz liczbę dni między sprawdzeniami. Można również wybrać, czy mają zostać zawieszone duplikaty. |
| **POWIADOMIENIA MDN tekst** | Nie | Określa domyślne powiadomienie o dyspozycji wiadomości (powiadomienia MDN), które ma zostać wysłane do nadawcy wiadomości. |
| **Wyślij powiadomienia MDN** | Nie | Określa, czy wysyłać synchroniczną MDNs dla odebranych komunikatów.  |
| **Wyślij podpisany powiadomienia MDN** | Nie | Określa, czy należy wysyłać podpisane wiadomości MDNs dla odebranych komunikatów. Jeśli wymagasz podpisywania, z listy **algorytm mikrofonu** wybierz algorytm, który ma być używany do podpisywania komunikatów. |
| **Wyślij powiadomienia MDN asynchroniczny** | Nie | Określa, czy ma być wysyłana asynchronicznie. W przypadku wybrania opcji asynchroniczny MDNs w polu **adres URL** Podaj adres URL, dla którego ma zostać wysłana mDNS. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Ustawienia wysyłania AS2

![Wybierz pozycję "Ustawienia wysyłania".](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| **Włącz podpisywanie komunikatów** | Nie | Określa, czy wszystkie wiadomości wychodzące muszą być podpisane cyfrowo. Jeśli wymagane jest podpisywanie, wybierz następujące wartości: <p>— Z listy **algorytm podpisywania** wybierz algorytm, który ma być używany do podpisywania wiadomości. <br>— Z listy **certyfikat** wybierz istniejący certyfikat prywatny partnera hosta do podpisywania wiadomości. Jeśli nie masz certyfikatu, Dowiedz się więcej o [dodawaniu certyfikatów](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Włącz szyfrowanie komunikatów** | Nie | Określa, czy wszystkie wiadomości wychodzące muszą być szyfrowane. Jeśli wymagane jest szyfrowanie, należy wybrać następujące wartości: <p>— Z listy **algorytm szyfrowania** wybierz algorytm certyfikatu publicznego partnera gościa, który ma być używany do szyfrowania wiadomości. <br>— Z listy **certyfikat** wybierz istniejący certyfikat prywatny partnera gościa na potrzeby szyfrowania wiadomości wychodzących. Jeśli nie masz certyfikatu, Dowiedz się więcej o [dodawaniu certyfikatów](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Włącz kompresję komunikatów** | Nie | Określa, czy wszystkie komunikaty wychodzące muszą być kompresowane. |
| **Unfold nagłówki HTTP** | Nie | Umieszcza nagłówek HTTP w `content-type` pojedynczym wierszu. |
| **Nazwa pliku transmisji w nagłówku MIME** | Nie | Określa, czy nazwa pliku ma zostać dołączona do nagłówka MIME. |
| **POWIADOMIENIA MDN żądania** | Nie | Określa, czy otrzymywać powiadomienia o dyspozycjach komunikatów (MDNs) dla wszystkich wychodzących komunikatów. |
| **POWIADOMIENIA MDN podpisanego żądania** | Nie | Określa, czy mają być odbierane podpisane usługi MDNs dla wszystkich wychodzących wiadomości. Jeśli wymagasz podpisywania, z listy **algorytm mikrofonu** wybierz algorytm, który ma być używany do podpisywania komunikatów. |
| **POWIADOMIENIA MDN asynchroniczne żądania** | Nie | Określa, czy ma być odbierana asynchronicznie. W przypadku wybrania opcji asynchroniczny MDNs w polu **adres URL** Podaj adres URL, dla którego ma zostać wysłana mDNS. |
| **Włącz NRR** | Nie | Określa, czy ma być wymagane potwierdzenie bez wyparcia (NRR). Ten atrybut komunikacyjny zawiera dowody, że dane zostały odebrane jako rozkierowane. |
| **Format algorytmu algorytmu SHA2** | Nie | Określa format algorytmu mikrofonu używany do podpisywania w nagłówkach wychodzących komunikatów AS2 lub powiadomienia MDN |
||||

## <a name="next-steps"></a>Następne kroki

[Wymienianie komunikatów AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

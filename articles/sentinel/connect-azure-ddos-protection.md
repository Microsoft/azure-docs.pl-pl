---
title: Łączenie Azure DDoS Protection danych z platformą Azure — wskaźnikiem
description: Dowiedz się, jak pozyskiwać Azure DDoS Protection dane do badania wskaźnikowego platformy Azure, aby je wyświetlić, przeanalizować i zbadać.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 8089b1e74e88db81c1c15ad2cbf2072abcfff241
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621349"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Łączenie danych z Azure DDoS Protection

Ataki rozproszonego typu "odmowa usługi" (DDoS) podejmują próbę wyczerpania zasobów aplikacji, co sprawia, że aplikacja jest niedostępna dla uprawnionych użytkowników. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu. [Usługa Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md), w połączeniu z najlepszymi rozwiązaniami dotyczącymi projektowania aplikacji, zapewnia niezawodną ochronę przed atakami DDoS. Możesz połączyć Azure DDoS Protection dzienniki z platformą Azure wskaźnikiem wydajności, umożliwiając wyświetlanie danych dziennika w skoroszytach, korzystanie z nich do tworzenia niestandardowych alertów i uwzględnianie ich w celu ulepszania badań. 

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Musisz mieć skonfigurowany [Plan ochrony usługi Azure DDoS Standard](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan).

- Musisz mieć skonfigurowaną [sieć wirtualną z włączoną usługą Azure DDoS Standard](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Połącz z Azure DDoS Protection
    
1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**.

1. Wybierz **Azure DDoS Protection** z galerii łączników danych, a następnie wybierz **Otwórz stronę łącznika** w okienku podglądu.

1. Włącz **dzienniki diagnostyczne** dla wszystkich publicznych adresów IP, których dzienników chcesz połączyć:

    1. Wybierz łącze **Otwórz ustawienia diagnostyki >** i wybierz z listy zasób **publicznego adresu IP** .

    1. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.

    1. Na ekranie **Ustawienia diagnostyki** :
       - Wprowadź nazwę w polu  **Nazwa ustawienia diagnostycznego** .

       - Zaznacz pole wyboru **Wyślij do log Analytics** . Zostaną wyświetlone dwa nowe pola poniżej. Wybierz odpowiednią **subskrypcję** i **log Analytics obszar roboczy** (gdzie znajduje się wskaźnik platformy Azure).

       - Zaznacz pola wyboru typów reguł, których dzienników chcesz pozyskać. Zalecamy **DDoSProtectionNotifications**, **DDoSMitigationFlowLogs** i **DDoSMitigationReports**.

    1. Kliknij przycisk **Zapisz** w górnej części ekranu. Powtórz ten proces dla wszelkich dodatkowych zapór (publicznych adresów IP), dla których włączono ochronę DDoS.

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów Azure DDoS Protection, wyszukaj ciąg **AzureDiagnostics**.

> [!NOTE]
>
> Po tym konkretnym łączniku danych wskaźniki stanu łączności (pasek koloru w galerii łączników danych i ikony połączeń obok nazw typów danych) będą wyświetlane jako *połączone* (kolor zielony) tylko wtedy, gdy dane zostały pozyskane w pewnym momencie w ciągu ostatnich dwóch tygodni. Po upływie dwóch tygodni od pozyskania danych łącznik zostanie wyświetlony jako odłączony. Po przejściu do większej ilości danych zostanie wyświetlony stan *połączona* .

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia dzienników Azure DDoS Protection z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

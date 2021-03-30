---
title: Łączenie danych zapory aplikacji sieci Web platformy Azure (WAF) z platformą Azure — wskaźnikiem
description: Dowiedz się, jak połączyć dane usługi Azure WAF z platformą Azure — wskaźnikiem.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: c554f3582e67622a5a1739c9e410328c902d491b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655905"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Łączenie danych z zapory aplikacji sieci Web platformy Azure (WAF)

Aplikacje sieci Web są coraz bardziej przeznaczone dla złośliwych ataków wykorzystujących często znane luki w zabezpieczeniach. Zapora aplikacji sieci Web (WAF) platformy Azure zapewnia centralną ochronę aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach, takimi jak iniekcja kodu i obsługa skryptów między lokacjami. Usługę Azure WAF można wdrożyć w ramach usługi [azure Application Gateway](../web-application-firewall/ag/ag-overview.md) , usługi Azure [Front-drzwiczk](../web-application-firewall/afds/afds-overview.md) i za pomocą zasad WAF usługi [Azure Content Delivery Network (](../web-application-firewall/cdn/cdn-overview.md) ta ostatnia jest obecnie dostępna w publicznej wersji zapoznawczej).
Dzienniki usługi Azure WAF można połączyć z platformą Azure wskaźnikiem wydajności, umożliwiając wyświetlanie danych dziennika w skoroszytach, korzystanie z nich do tworzenia niestandardowych alertów i uwzględnianie ich w celu usprawnienia badania.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

## <a name="connect-to-azure-waf"></a>Nawiązywanie połączenia z usługą Azure WAF

### <a name="instructions-tab"></a>Karta instrukcje

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**.

1. Wybierz pozycję **Zapora aplikacji sieci Web platformy Azure (WAF)** z galerii łączniki danych, a następnie wybierz pozycję **Otwórz stronę łącznika** w okienku podglądu.

1. Wybierz łącze dla typu zasobu WAF, którego dzienników chcesz połączyć — **otwórz Application Gateway >zasobów**, **Otwórz >zasobów front-drzwi** lub **Otwórz Content Delivery Network (CDN) WAF zasad >** — a raz na ekranie Lista zasobów wybierz zasób WAF z listy.

    1. Z menu nawigacji zasobu WAF wybierz pozycję **Ustawienia diagnostyczne**.

    1. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne** u dołu listy.

    1. Na ekranie **Ustawienia diagnostyczne** wpisz nazwę w polu **Nazwa ustawień diagnostycznych** .

    1. Kliknij pole wyboru **Wyślij do log Analytics** . Zostaną wyświetlone dwa nowe pola poniżej. Wybierz odpowiednią **subskrypcję** i **log Analytics obszar roboczy** (gdzie znajduje się wskaźnik platformy Azure).

    1. Kliknij pola wyboru typów reguł, których dzienników chcesz pozyskać. Nasze zalecenia dotyczące każdego typu zasobu:

        | Zasób | Dzienniki do wybrania do pozyskiwania |
        |----------|------------------------------|
        | Application Gateway | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | Zasady WAFymi sieci CDN      | WebApplicationFirewallLogs |
        |

    1. Wybierz pozycję **Zapisz**.

### <a name="next-steps-tab"></a>Karta następne kroki

- Zapoznaj się z zalecanymi skoroszytami, przykładami zapytań i szablonami reguł analizy, które są powiązane z łącznikiem danych **zapory aplikacji sieci Web platformy Azure** , aby uzyskać wgląd w dane dziennika usługi Azure WAF.

- Aby wykonać zapytanie dotyczące danych usługi Azure WAF w **dziennikach**, wpisz **AzureDiagnostics** w oknie zapytania.

> [!NOTE]
>
> Po tym konkretnym łączniku danych wskaźniki stanu łączności (pasek koloru w galerii łączników danych i ikony połączeń obok nazw typów danych) będą wyświetlane jako *połączone* (kolor zielony) tylko wtedy, gdy dane zostały pozyskane w pewnym momencie w ciągu ostatnich dwóch tygodni. Po upływie dwóch tygodni od pozyskania danych łącznik zostanie wyświetlony jako odłączony. Po przejściu do większej ilości danych zostanie wyświetlony stan *połączona* .

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia dzienników usługi Azure WAF z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
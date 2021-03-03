---
title: Połącz swoje rozwiązania chroniące przed wyłudzaniem informacji Agari i ochrony marki z platformą Azure Microsoft Docs
description: Dowiedz się, w jaki sposób używać łącznika ochrony przed phishingiem Agari i programu do ściągania swoich dzienników do platformy Azure. Wyświetlaj dane Agari w skoroszytach, twórz Alerty i ulepszaj badanie.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a60a0291d6669b2a9115dffa8e0d4d63fae4a440
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724442"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Połącz swoje rozwiązania chroniące przed wyłudzaniem informacji Agari i ochrony marki z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik ochrony przed wyłudzaniem informacji Agari i ochrona przed markami jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

Łącznik ochrony przed Agari phishingiem umożliwia łatwe łączenie dzienników ochrony marki i rozwiązań obronnych z witryną Azure, aby można było wyświetlać dane w skoroszytach, wysyłać zapytania do tworzenia niestandardowych alertów i uwzględniać je w celu usprawnienia badania. Rozwiązania Agari integrują się z usługą Azure wskaźnikiem przy użyciu Azure Functions i interfejsu API REST.

Dodatkowo ochrona marki i klient z odpowiedzią na wyłudzanie informacji mogą korzystać z funkcji udostępniania analizy zagrożeń za pośrednictwem interfejs API programu Graph zabezpieczeń.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej podano następujące wymagania, aby połączyć rozwiązania chroniące przed wyłudzaniem informacji Agari i ochronę przed markami na platformie Azure.

- Uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Uprawnienia do odczytu kluczy współużytkowanych w obszarze roboczym. [Dowiedz się więcej o kluczach obszarów roboczych](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Uprawnienia do odczytu i zapisu w Azure Functions, aby utworzyć aplikacja funkcji. [Dowiedz się więcej o Azure Functions](../azure-functions/index.yml).

- Upewnij się, że masz **Identyfikator klienta** Agari oraz **klucze tajne** (identyczne we wszystkich rozwiązaniach Agari). Instrukcje można znaleźć w [witrynie Agari Developers](https://developers.agari.com/agari-platform/docs/quick-start) .

## <a name="configure-and-connect-agari-solutions"></a>Konfigurowanie i łączenie rozwiązań Agari 

Rozwiązania Agari umożliwiają integrację i eksportowanie dzienników bezpośrednio do funkcji wskaźnikowej platformy Azure przy użyciu aplikacja funkcji platformy Azure.

> [!NOTE]
> Ten łącznik używa Azure Functions do łączenia się z rozwiązaniami Agari w celu ściągania ich dzienników do platformy Azure. Może to skutkować dodatkowymi kosztami pozyskiwania danych. Aby uzyskać szczegółowe informacje, zobacz stronę z [cennikiem Azure Functions](https://azure.microsoft.com/pricing/details/functions/) .

1. **Zbierz poświadczenia interfejsu API Agari:** 

    Upewnij się, że masz **Identyfikator klienta** Agari oraz **klucze tajne**. Instrukcje można znaleźć w [witrynie Agari Developers](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials).

1. **Obowiązkowe Włącz interfejs API programu Graph zabezpieczeń:** 

    Agari aplikacja funkcji umożliwia udostępnianie analizy zagrożeń za pomocą funkcji wskaźnikowej platformy Azure za pośrednictwem interfejs API programu Graph zabezpieczeń. Aby użyć tej funkcji, należy włączyć [Łącznik platformy analizy zagrożeń wskaźnikiem](connect-threat-intelligence.md) , a także [zarejestrować aplikację](/graph/auth-register-app-v2) w Azure Active Directory.

    Ten proces zapewnia trzy informacje do użycia podczas wdrażania aplikacja funkcji poniżej: **Identyfikator dzierżawy grafu**, **Identyfikator klienta grafu** i **klucz tajny klienta grafu**.

1. **Wdróż łącznik i powiązane aplikacja funkcji platformy Azure:** 

    1. W portalu wskaźnikowym platformy Azure wybierz pozycję **Łączniki danych**. Wybierz pozycję **Agari phishing obrony i ochrona marki (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznik**.

    1. W obszarze **Konfiguracja** Skopiuj **Identyfikator obszaru roboczego** i **klucz podstawowy** platformy Azure, a następnie wklej je do nich.

    1. Wybierz pozycję **Wdróż na platformie Azure**. (Może być konieczne przewinięcie w dół w celu znalezienia przycisku).

    1. Zostanie wyświetlony ekran **wdrożenie niestandardowe** .

        - Wprowadź **Identyfikator klienta** Agari i **klucz tajny klienta** (klucze tajne)

        - Wprowadź **Identyfikator obszaru roboczego** i **klucz obszaru roboczego** platformy Azure (klucz podstawowy), który został skopiowany i pozostawiony.

        - Wybierz opcję **prawda** lub **Fałsz** dla rozwiązań Agari, dla których masz aktywne subskrypcje.

        - Jeśli utworzono aplikację platformy Azure, aby udostępnić IoCs z platformą Azure, przy użyciu interfejs API programu Graph zabezpieczeń, wybierz **wartość true** dla opcji **Włącz udostępnianie wykresu zabezpieczeń** , a następnie wprowadź **Identyfikator dzierżawy wykresu**, **Identyfikator klienta grafu** i **klucz tajny klienta grafu**.

    1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji kliknij pozycję **Utwórz**.

1. **Przypisz odpowiednie uprawnienia do aplikacja funkcji:**

    Łącznik Agari używa zmiennej środowiskowej do przechowywania sygnatur czasowych dostępu do dziennika. Aby aplikacja mogła zapisywać w tej zmiennej, uprawnienia muszą być przypisane do tożsamości przypisanej do systemu.

    1. W Azure Portal przejdź do **aplikacja funkcji**.

    1. W bloku **aplikacja funkcji** wybierz Aplikacja funkcji z listy, a następnie wybierz pozycję **tożsamość** w obszarze **Ustawienia** w menu nawigacji aplikacja funkcji.

    1. Na karcie **przypisane do systemu** Ustaw **stan** na **włączone**. 

    1. Wybierz pozycję **Zapisz** i zostanie wyświetlony przycisk **przydziały ról platformy Azure** . Kliknij go.

    1. Na ekranie **przypisania ról platformy Azure** wybierz pozycję **Dodaj przypisanie roli**. Ustaw **zakres** na **subskrypcję**, wybierz subskrypcję z listy rozwijanej **subskrypcja** i ustaw **rolę** **właściciel danych konfiguracji aplikacji**. 

    1. Wybierz pozycję **Zapisz**.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w obszarze *CustomLogs*, w następujących tabelach: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Aby zbadać dane rozwiązań Agari, wprowadź jedną z powyższych nazw tabel w oknie zapytania.

Niektóre przydatne przykładowe zapytania znajdują się na karcie **następne kroki** na stronie łącznik.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z rozwiązaniami ochrony przed Agari phishingiem i ochroną marki na platformie Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
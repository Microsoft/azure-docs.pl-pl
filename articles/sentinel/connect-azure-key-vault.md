---
title: Łączenie Azure Key Vault dzienników diagnostycznych z platformą Azure — wskaźnikiem
description: Dowiedz się, w jaki sposób używać Azure Policy do łączenia dzienników diagnostyki Azure Key Vault z platformą Azure — wskaźnikiem.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 56587ae91de086cccb7cc85c125f935be2a56f73
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102491284"
---
# <a name="connect-azure-key-vault-diagnostics-and-auditing-logs"></a>Łączenie Azure Key Vault dzienników diagnostycznych i inspekcji

Azure Key Vault to usługa w chmurze umożliwiająca bezpieczne przechowywanie i uzyskiwanie dostępu do wpisów tajnych. Wpis tajny to wszystko, co umożliwia ścisłe sterowanie dostępem, takich jak klucze interfejsu API, hasła, certyfikaty lub klucze kryptograficzne.

Ten łącznik umożliwia przesyłanie strumieniowe dzienników diagnostyki Azure Key Vault do badania wskaźnikowego platformy Azure, dzięki czemu można stale monitorować aktywność we wszystkich wystąpieniach.

Dowiedz się więcej o [monitorowaniu Azure Key Vault](../azure-monitor/insights/key-vault-insights-overview.md) i informacje o [telemetrii diagnostyki Azure Key Vault](../key-vault/general/logging.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby pozyskać Azure Key Vault dzienniki na platformie Azure — wskaźnik:

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Aby użyć Azure Policy do zastosowania zasad przesyłania strumieniowego dziennika do zasobów Azure Key Vault, musisz mieć przypisaną rolę właściciela dla zakresu przypisania zasad.

## <a name="connect-to-azure-key-vault"></a>Połącz z Azure Key Vault

Ten łącznik używa Azure Policy do zastosowania pojedynczego Azure Key Vault konfiguracji przesyłania strumieniowego dzienników do kolekcji wystąpień zdefiniowanej jako zakres. Możesz zobaczyć typy dzienników pozyskane z Azure Key Vault po lewej stronie łącznika w obszarze **typy danych**.

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**.

1. Wybierz **Azure Key Vault** z galerii łączników danych, a następnie wybierz **Otwórz stronę łącznika** w okienku podglądu.

1. W sekcji **Konfiguracja** na stronie łącznik rozwiń węzeł **Włączanie dzienników diagnostycznych na Azure Key Vault**.

1. Wybierz przycisk **Kreator uruchamiania Przypisywania Azure Policy** .

    Zostanie otwarty Kreator przypisania zasad, gotowy do utworzenia nowych zasad o nazwie **Wdróż-Skonfiguruj ustawienia diagnostyczne dla Azure Key Vault do log Analytics obszaru roboczego**.

    1. Na karcie **podstawy** kliknij przycisk z trzema kropkami w obszarze **zakres** , aby wybrać subskrypcję (i opcjonalnie grupę zasobów). Możesz również dodać opis.

    1. Na karcie **Parametry** wybierz obszar roboczy Azure wskaźnikowy z listy rozwijanej **obszar roboczy log Analytics** . Pozostałe pola rozwijane reprezentują dostępne typy dzienników diagnostycznych. Pozostaw oznaczone jako "true" wszystkie typy dzienników, które chcesz pozyskać.

    1. Aby zastosować zasady do istniejących zasobów, wybierz kartę **korygowanie** i zaznacz pole wyboru **Utwórz zadanie korygujące** .

    1. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**. Twoje zasady są teraz przypisane do wybranego zakresu.

> [!NOTE]
>
> Po tym konkretnym łączniku danych wskaźniki stanu łączności (pasek koloru w galerii łączników danych i ikony połączeń obok nazw typów danych) będą wyświetlane jako *połączone* (kolor zielony) tylko wtedy, gdy dane zostały pozyskane w pewnym momencie w ciągu ostatnich dwóch tygodni. Po upływie dwóch tygodni od pozyskania danych łącznik zostanie wyświetlony jako odłączony. Po przejściu do większej ilości danych zostanie wyświetlony stan *połączona* .

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure Key Vault z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

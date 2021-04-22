---
title: Łączenie dzienników diagnostycznych konta usługi Azure Storage z Azure Sentinel
description: Dowiedz się, jak używać Azure Policy do łączenia dzienników diagnostycznych konta usługi Azure Storage z Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: b4260d31b587f2a20d7bc9d4c4e3e6a0d225a416
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879131"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Łączenie dzienników diagnostycznych konta usługi Azure Storage

Konto usługi Azure Storage to rozwiązanie w chmurze dla nowoczesnych scenariuszy magazynowania danych. Zawiera wszystkie obiekty danych: obiekty blob, pliki, kolejki, tabele i dyski.

Ten łącznik umożliwia przesyłanie strumieniowe dzienników diagnostycznych kont usługi Azure Storage do usługi Azure Sentinel, co pozwala na ciągłe monitorowanie aktywności i wykrywanie zagrożeń bezpieczeństwa we wszystkich zasobach usługi Azure Storage w całej organizacji.

Dowiedz się więcej o [monitorowaniu konta usługi Azure Storage.](../storage/common/storage-analytics-logging.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby pozysować dzienniki diagnostyczne konta usługi Azure Storage w Azure Sentinel:

- Musisz mieć uprawnienia do odczytu i zapisu w Azure Sentinel roboczym.

- Aby użyć Azure Policy do zastosowania zasad przesyłania strumieniowego dzienników do zasobów usługi Azure Storage, musisz mieć rolę właściciela dla zakresu przypisania zasad.

## <a name="connect-to-azure-storage-account"></a>Nawiązywanie połączenia z kontem usługi Azure Storage

Ten łącznik używa Azure Policy, aby zastosować konfigurację przesyłania strumieniowego pojedynczego dziennika do kolekcji zasobów konta usługi Azure Storage zdefiniowanych jako zakres. Dostępne typy metryk i dziennika usługi Azure Storage są dostępne po lewej stronie łącznika w obszarze **Typy danych.**

1. W Azure Sentinel nawigacji wybierz pozycję **Łączniki danych.**

1. Wybierz **pozycję Konto usługi Azure Storage** z galerii łączników danych, a następnie wybierz pozycję Otwórz stronę **łącznika** w okienku podglądu.

1. W sekcji **Konfiguracja** na stronie łącznika rozwiń sekcję Przesyłanie strumieniowe dzienników **diagnostycznych z konta usługi Azure Storage na dużą skalę.**

1. Wybierz przycisk **Kreatora Azure Policy przypisania** aplikacji.

    Zostanie otwarty kreator przypisania zasad gotowy do utworzenia nowych zasad o nazwie Konfigurowanie ustawień diagnostycznych dla kont **magazynu w obszarze roboczym usługi Log Analytics.**

    1. Na karcie **Podstawy** kliknij przycisk z trzema kropkami obok opcji **Zakres,** aby wybrać subskrypcję (i opcjonalnie grupę zasobów). Możesz również dodać opis.

    1. Na **karcie** Parametry:
        - Wybierz obszar Azure Sentinel z listy rozwijanej Obszar roboczy usługi **Log Analytics.**
        - Wybierz z listy **rozwijanej Usługi** magazynu do wdrożenia typy zasobów magazynu (plik, tabela, kolejka itp.), w których chcesz wdrożyć ustawienia diagnostyczne.
        - Pozostaw **pola Nazwa ustawienia** i **Efekt** bez wartości.
        - Pozostałe pola listy rozwijanej reprezentują dostępne typy dzienników diagnostycznych i metryk. Pozostaw oznaczone jako "True" wszystkie typy, które chcesz pozytać.

    1. Powyższe kroki zastosują zasady do wszystkich przyszłych zasobów magazynu. Aby zastosować zasady do istniejących zasobów, wybierz **kartę Korygowanie** i zaznacz pole wyboru Utwórz zadanie **korygowania.**

    1. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**. Twoje zasady są teraz przypisane do wybranego zakresu.

> [!NOTE]
>
> W przypadku tego konkretnego łącznika danych wskaźniki stanu łączności (pasek kolorów w galerii łączników danych  i ikony połączeń obok nazw typów danych) będą wyświetlane jako połączone (zielony) tylko wtedy, gdy dane zostały pozyskane w pewnym momencie w ciągu ostatnich 14 dni. Po 14 dniach bez pozyskiwania danych łącznik będzie pokazywany jako odłączony. Gdy pojawi się więcej danych, *zostanie zwrócony* stan połączenia.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie opisano sposób łączenia konta usługi Azure Storage z usługą Azure Sentinel. Aby dowiedzieć się więcej na Azure Sentinel, zobacz następujące artykuły:

- Dowiedz się, [jak uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Wprowadzenie do [wykrywania zagrożeń za pomocą Azure Sentinel](tutorial-detect-threats-built-in.md).

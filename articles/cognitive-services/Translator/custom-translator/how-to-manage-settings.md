---
title: Jak zarządzać ustawieniami? — Translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Zarządzanie ustawieniami, tworzenie obszaru roboczego, udostępnianie obszaru roboczego i zarządzanie kluczami subskrypcji w usłudze translator niestandardowym.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: fe2c0eed2d476d01dffd22a61dd431140f26c475
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587248"
---
# <a name="how-to-manage-settings"></a>Jak zarządzać ustawieniami

Na stronie niestandardowe ustawienia usługi translator można utworzyć nowy obszar roboczy, udostępnić obszar roboczy oraz dodać lub zmodyfikować klucz subskrypcji tłumaczenia firmy Microsoft.

Aby uzyskać dostęp do strony ustawień:

1. Zaloguj się do portalu usługi [Custom translator](https://portal.customtranslator.azure.ai/) .
2. W portalu usługi tłumaczenia niestandardowego kliknij ikonę koła zębatego na pasku bocznym.

    ![Link Ustawienia](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Kojarzenie subskrypcji usługi Microsoft Translator

Aby uczenie lub wdrożenie modeli było możliwe, musisz dysponować kluczem subskrypcji usługi Translator skojarzonym z Twoim obszarem roboczym.

Jeśli nie masz subskrypcji, wykonaj następujące czynności:

1. Zasubskrybuj translator. W tym artykule przedstawiono sposób subskrybowania usługi Translator.
2. Zanotuj klucz subskrypcji usługi Translator. Klucz1 lub klucz2 są akceptowalne.
3. Wróć do portalu usługi Custom translator.

### <a name="add-existing-key"></a>Dodaj istniejący klucz

1.  Przejdź do strony "Ustawienia" w obszarze roboczym.
2.  Kliknij pozycję Dodaj klucz

    ![Jak dodać klucz subskrypcji](media/how-to/how-to-add-subscription-key.png)

3. W oknie dialogowym wprowadź klucz subskrypcji usługi translator, a następnie kliknij przycisk "Dodaj".

    ![Jak dodać klucz subskrypcji](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Po dodaniu klucza można w dowolnym momencie zmodyfikować lub usunąć klucz.

    ![Klucz subskrypcji po dodaniu](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Zarządzanie obszarem roboczym

Obszar roboczy to obszar roboczy służący do redagowania i kompilowania niestandardowego systemu tłumaczenia. Obszar roboczy może zawierać wiele projektów, modeli i dokumentów.

Jeśli inna część pracy musi być udostępniana innym osobom, może być przydatne utworzenie wielu obszarów roboczych.

## <a name="create-a-new-workspace"></a>Tworzenie nowego obszaru roboczego

1.  Przejdź do strony obszar roboczy "Ustawienia".
2.  Kliknij przycisk "nowy obszar roboczy" w sekcji "Tworzenie nowego obszaru roboczego".

    ![Utwórz nowy obszar roboczy](media/how-to/create-new-workspace.png)

4.  W oknie dialogowym wprowadź nazwę nowego obszaru roboczego.
5.  Kliknij przycisk "Utwórz".

    ![Okno dialogowe Tworzenie nowego obszaru roboczego](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Udostępnianie obszaru roboczego

W przypadku translatora niestandardowego możesz udostępnić obszar roboczy innym osobom, jeśli inna część pracy musi być udostępniana innym osobom.

1.  Przejdź do strony obszar roboczy "Ustawienia".
2.  Kliknij przycisk "Udostępnij" w sekcji "Ustawienia udostępniania".

    ![Udostępnianie obszaru roboczego](media/how-to/share-workspace.png)

3.  W oknie dialogowym wprowadź rozdzieloną przecinkami listę adresów e-mail, które mają być udostępniane przez ten obszar roboczy. Upewnij się, że udostępniasz adres e-mail, za pomocą którego osoba loguje się do usługi Custom translator przy użyciu programu. Następnie wybierz odpowiedni poziom uprawnień do udostępniania.

4.  Jeśli obszar roboczy ma nadal domyślną nazwę "Mój obszar roboczy", musisz go zmienić przed udostępnieniem obszaru roboczego.
5.  Kliknij przycisk "Zapisz".

## <a name="sharing-permissions"></a>Uprawnienia do udostępniania

1.  **Czytelnik:** Czytnik w obszarze roboczym będzie mógł wyświetlić wszystkie informacje w obszarze roboczym.

2.  **Edytor:** Edytor w obszarze roboczym będzie mógł dodawać dokumenty, przeszkolić modele i usuwać dokumenty i projekty. Mogą oni dodać klucz subskrypcji, ale nie może modyfikować osoby, którym udostępniono obszar roboczy, usunąć obszar roboczy lub zmienić nazwę obszaru roboczego.

3.  **Właściciel:** Właściciel ma pełne uprawnienia do obszaru roboczego.

## <a name="change-sharing-permission"></a>Uprawnienie do zmiany udostępniania

Po udostępnieniu obszaru roboczego w sekcji "Ustawienia udostępniania" są wyświetlane wszystkie adresy e-mail, które są udostępniane przez ten obszar roboczy. Jeśli masz dostęp właściciela do obszaru roboczego, możesz zmienić istniejące uprawnienia do udostępniania dla każdego adresu e-mail.

1.  W sekcji "Ustawienia udostępniania" poszczególnych wiadomości e-mail menu rozwijane zawiera bieżący poziom uprawnień.

2.  Kliknij menu rozwijane i wybierz nowy poziom uprawnień, który chcesz przypisać do tego adresu e-mail.

    ![Udostępnianie ustawień uprawnień](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak przeprowadzić migrację obszaru roboczego i projektu](how-to-migrate.md) z usługi [Microsoft Translator Hub](https://hub.microsofttranslator.com)

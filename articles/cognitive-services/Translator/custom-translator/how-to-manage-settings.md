---
title: Jak zarządzać ustawieniami? — Translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Zarządzanie ustawieniami, tworzenie obszaru roboczego, udostępnianie obszaru roboczego i zarządzanie kluczami subskrypcji w usłudze translator niestandardowym.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 49801eddd748a88109bb7f6d075def03cd798754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895785"
---
# <a name="how-to-manage-settings"></a>Jak zarządzać ustawieniami

Na stronie Ustawienia translatora niestandardowego można udostępnić obszar roboczy, zmodyfikować klucz subskrypcji usługi Translator i usunąć obszar roboczy.

Aby uzyskać dostęp do strony ustawień:

1. Zaloguj się do portalu usługi [Custom translator](https://portal.customtranslator.azure.ai/) .
2. W portalu usługi tłumaczenia niestandardowego kliknij ikonę koła zębatego na pasku bocznym.

    ![Link Ustawienia](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Kojarzenie subskrypcji usługi translator

Aby uczenie lub wdrożenie modeli było możliwe, musisz dysponować kluczem subskrypcji usługi Translator skojarzonym z Twoim obszarem roboczym.

Jeśli nie masz subskrypcji, wykonaj następujące czynności:

1. Subskrybuj, aby utworzyć zasób usługi Translator. Postępuj zgodnie z instrukcjami, [Aby zarejestrować się w usłudze translator](../translator-how-to-signup.md) , aby subskrybować i uzyskać klucz usługi Translator.
2. Zanotuj klucz subskrypcji usługi Translator. Klucz1 lub klucz2 są akceptowalne.
3. Wróć do portalu usługi Custom translator.

## <a name="create-a-new-workspace"></a>Tworzenie nowego obszaru roboczego

1. Kliknij przycisk "+ Utwórz obszar roboczy" w pasku bocznym "tłumaczenie niestandardowe".

    ![Utwórz nowy obszar roboczy](media/how-to/create-new-workspace.png)

2. W oknie dialogowym wprowadź nazwę nowego obszaru roboczego.
3. Kliknij przycisk "dalej".
4. Wybierz typ subskrypcji.
5. Wybierz region subskrypcji. Podczas tworzenia klucza zasobu usługi Translator region musi być zgodny z wybranym regionem.
6. Wprowadź klucz dla subskrypcji usługi translator, a następnie kliknij przycisk "Zapisz".

    ![Okno dialogowe Tworzenie nowego obszaru roboczego](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>Usługa translatora niestandardowego nie obsługuje tworzenia obszaru roboczego dla zasobu interfejs API tłumaczenia tekstu w usłudze Translator (vel Klucz subskrypcji platformy Azure), który został utworzony w ramach [włączonej sieci wirtualnej](../../../api-management/api-management-using-with-vnet.md).

### <a name="modify-existing-key"></a>Modyfikuj istniejący klucz

1. Przejdź do strony "Ustawienia" w obszarze roboczym.
2. Kliknij pozycję Zmień klucz

    ![Jak dodać klucz subskrypcji](media/how-to/how-to-add-subscription-key.png)

3. W oknie dialogowym wprowadź klucz subskrypcji usługi translator, a następnie kliknij przycisk "Zapisz".

    ![Dodawanie klucza subskrypcji — okno dialogowe](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>Zarządzanie obszarem roboczym

Obszar roboczy to obszar roboczy służący do redagowania i kompilowania niestandardowego systemu tłumaczenia. Obszar roboczy może zawierać wiele projektów, modeli i dokumentów.

Jeśli inna część pracy musi być udostępniana innym osobom, może być przydatne utworzenie wielu obszarów roboczych.

## <a name="share-your-workspace"></a>Udostępnianie obszaru roboczego

W przypadku translatora niestandardowego możesz udostępnić obszar roboczy innym osobom, jeśli inna część pracy musi być udostępniana innym osobom.

1. Przejdź do strony obszar roboczy "Ustawienia".
2. Kliknij przycisk "Dodaj osoby" w sekcji "Ustawienia udostępniania".

    ![Udostępnianie obszaru roboczego](media/how-to/share-workspace.png)

3. W oknie dialogowym wprowadź rozdzieloną przecinkami listę adresów e-mail, które mają być udostępniane przez ten obszar roboczy. Upewnij się, że udostępniasz adres e-mail, za pomocą którego osoba loguje się do usługi Custom translator przy użyciu programu. Następnie wybierz odpowiedni poziom uprawnień do udostępniania i kliknij przycisk "Zapisz".

    ![Okno dialogowe udostępnianie obszaru roboczego](media/how-to/share-workspace-dialog.png)

4. Jeśli obszar roboczy ma nadal domyślną nazwę "Mój obszar roboczy", musisz go zmienić przed udostępnieniem obszaru roboczego.
5. Kliknij przycisk "Zapisz".

## <a name="sharing-permissions"></a>Uprawnienia do udostępniania

1. **Czytelnik:** Czytnik w obszarze roboczym będzie mógł wyświetlić wszystkie informacje w obszarze roboczym.

2. **Edytor:** Edytor w obszarze roboczym będzie mógł dodawać dokumenty, przeszkolić modele i usuwać dokumenty i projekty. Mogą oni dodać klucz subskrypcji, ale nie może modyfikować osoby, którym udostępniono obszar roboczy, usunąć obszar roboczy lub zmienić nazwę obszaru roboczego.

3. **Właściciel:** Właściciel ma pełne uprawnienia do obszaru roboczego.

## <a name="change-sharing-permission"></a>Uprawnienie do zmiany udostępniania

Po udostępnieniu obszaru roboczego w sekcji "Ustawienia udostępniania" są wyświetlane wszystkie adresy e-mail, które są udostępniane przez ten obszar roboczy. Jeśli masz dostęp właściciela do obszaru roboczego, możesz zmienić istniejące uprawnienia do udostępniania dla każdego adresu e-mail.

1. W sekcji "Ustawienia udostępniania" poszczególnych wiadomości e-mail menu rozwijane zawiera bieżący poziom uprawnień.

2. Kliknij menu rozwijane i wybierz nowy poziom uprawnień, który chcesz przypisać do tego adresu e-mail.

    ![Udostępnianie ustawień uprawnień](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>Przypinanie obszaru roboczego

Pierwszy utworzony obszar roboczy jest domyślnie przypięty. Za każdym razem, gdy logujesz się, przypięty obszar roboczy jest wyświetlany podczas ładowania lokacji. Jeśli utworzono wiele obszarów roboczych i wolisz utworzyć jedną z nich podczas logowania, musisz przypiąć ją.

1. Na pasku bocznym kliknij nazwę obszaru roboczego, który chcesz przypiąć.
2. Przejdź do strony "Ustawienia" w obszarze roboczym.
3. Kliknij ikonę pinezki.

    ![Przypnij obszar roboczy](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak utworzyć obszar roboczy i projekty](workspace-and-project.md)
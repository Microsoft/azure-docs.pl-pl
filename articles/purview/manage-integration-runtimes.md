---
title: Tworzenie i zarządzanie środowiskami Integration Runtime
description: W tym artykule opisano kroki tworzenia środowiska Integration Runtime i zarządzania nim w usłudze Azure kontrolą.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 0d365787ea3603ef0adb8ad0b48bef9792ffb003
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553654"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Tworzenie własnego środowiska Integration Runtime i zarządzanie nim

W tym artykule opisano sposób tworzenia własnego środowiska Integration Runtime (SHIR) i zarządzania nim w celu ułatwienia skanowania źródeł danych.

## <a name="create-a-self-hosted-integration-runtime"></a>Tworzenie własnego środowiska Integration Runtime

1. Na stronie głównej programu kontrolą Studio wybierz pozycję **centrum zarządzania** w okienku nawigacji po lewej stronie.

2. W obszarze **źródła i skanowanie** w okienku po lewej stronie wybierz pozycję **Integration Runtimes**, a następnie wybierz pozycję **+ Nowy**.

    :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Kliknij pozycję IR.":::

3. Na stronie **Konfiguracja środowiska Integration Runtime** wybierz pozycję **samodzielna** , aby utworzyć Self-Hosted IR, a następnie wybierz pozycję **Kontynuuj**.

    :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Utwórz nowy SHIR.":::

4. Wprowadź nazwę dla swojego środowiska IR, a następnie wybierz pozycję Utwórz.

5. Na stronie **ustawienia Integration Runtime** postępuj zgodnie z instrukcjami w sekcji **Konfiguracja ręczna** . Konieczne będzie pobranie środowiska Integration Runtime z witryny pobierania na maszynę wirtualną lub na maszynę, na której zamierzasz ją uruchomić.

    :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Pobierz klucz":::

    a. Skopiuj i Wklej klucz uwierzytelniania.
        
    b. Pobierz własne środowisko Integration Runtime z [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) na lokalnym komputerze z systemem Windows. Uruchom instalatora.
        
    c. Na stronie **rejestrowanie Integration Runtime (Self-Hosted)** wklej jeden z dwóch kluczy, które zostały zapisane wcześniej, a następnie wybierz pozycję **zarejestruj**.

    :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="klucz wejściowy.":::

    d. Na stronie **nowy węzeł Integration Runtime (Self-Hosted)** wybierz pozycję **Zakończ**.

6. Po pomyślnym zarejestrowaniu własnego środowiska Integration Runtime zostanie wyświetlone następujące okno:

    :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="pomyślnie zarejestrowano.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Zarządzanie własnym środowiskiem Integration Runtime

Możesz edytować własne środowisko Integration Runtime, przechodząc do **środowiska Integration Runtime** w **centrum zarządzania**, wybierając środowisko IR, a następnie klikając pozycję Edytuj. Teraz możesz zaktualizować opis, skopiować klucz lub ponownie wygenerować nowe klucze.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Edytuj środowisko IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Edytuj szczegóły podczerwieni.":::

Możesz usunąć własne środowisko Integration Runtime, przechodząc do **środowiska Integration Runtime** w centrum zarządzania, wybierając środowisko IR, a następnie klikając przycisk **Usuń**. Po usunięciu podczerwieni wszystkie trwające przez niego skanowania nie powiodą się.

## <a name="next-steps"></a>Następne kroki

* [Jak skany wykrywają usunięte zasoby](concept-detect-deleted-assets.md)

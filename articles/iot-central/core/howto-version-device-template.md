---
title: Informacje o wersji szablonów urządzeń dla aplikacji IoT Central platformy Azure | Microsoft Docs
description: Wykonaj iterację szablonów urządzeń, tworząc nowe wersje i bez wpływu na urządzenia połączone na żywo
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 37c7bc99881c8d1106c8464cfe18c9e63b8a1b02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756723"
---
# <a name="create-a-new-device-template-version"></a>Utwórz nową wersję szablonu urządzenia

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

Usługa Azure IoT Central umożliwia szybkie opracowywanie aplikacji IoT. Możesz szybko wykonać iterację w projektach szablonów urządzeń, dodając, edytując lub usuwając możliwości, widoki i dostosowania urządzeń. Po opublikowaniu szablonu urządzenia model możliwości urządzenia będzie widoczny jako **opublikowany** z ikonami blokowania obok modelu. Aby wprowadzić zmiany w modelu możliwości urządzenia, należy utworzyć nową wersję szablonu urządzenia. W każdej chwili można edytować właściwości, dostosowania i widoki chmury bez konieczności podania wersji szablonu urządzenia. Po zapisaniu którejkolwiek z tych zmian można opublikować szablon urządzenia, aby udostępnić najnowsze zmiany dla operatora do wyświetlenia w Device Explorer.

> [!NOTE]
> Aby dowiedzieć się więcej na temat tworzenia szablonu urządzenia [, zobacz Konfigurowanie szablonu urządzenia i zarządzanie nim](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Dodawanie dostosowań do szablonu urządzenia bez obsługi wersji

Niektóre elementy możliwości urządzenia można edytować bez konieczności korzystania z wersji szablonu i interfejsów urządzeń. Na przykład niektóre z tych pól obejmują nazwę wyświetlaną, typ semantyczny, wartość minimalną, wartość maksymalną, miejsca dziesiętne, kolor, jednostkę, jednostkę wyświetlania, komentarz i opis. Aby dodać jedno z następujących dostosowań:

1. Przejdź do strony **Szablony urządzeń** .
1. Wybierz szablon urządzenia, który chcesz dostosować.
1. Wybierz kartę **Dostosowywanie** .
1. Wszystkie możliwości zdefiniowane w modelu możliwości urządzenia zostaną wyświetlone w tym miejscu. Wszystkie pola, które można edytować w tym miejscu, mogą być zapisywane i używane w całej aplikacji bez konieczności korzystania z wersji szablonu urządzenia. Jeśli istnieją pola, które chcesz edytować, są przeznaczone tylko do odczytu, aby je zmienić, musisz mieć wersję szablonu urządzenia. Wybierz pole, które chcesz edytować, a następnie wprowadź nowe wartości.
1. Kliknij przycisk **Zapisz**. Teraz te wartości zastępują wszystkie elementy, które zostały początkowo zapisane w szablonie urządzenia i będą używane w całej aplikacji.

## <a name="versioning-a-device-template"></a>Przechowywanie wersji szablonu urządzenia

Utworzenie nowej wersji szablonu urządzenia spowoduje utworzenie wersji roboczej szablonu, w którym można edytować model możliwości urządzenia. Wszystkie opublikowane interfejsy zostaną opublikowane, dopóki nie zostaną one w wersji pojedynczej. Aby można było zmodyfikować opublikowany interfejs, należy najpierw utworzyć nową wersję szablonu urządzenia.

W przypadku próby edytowania części modelu możliwości urządzenia, którego nie można edytować, w sekcji dostosowania szablonu urządzenia należy tylko wersja. 

Aby uzyskać wersję szablonu urządzenia:

1. Przejdź do strony **Szablony urządzeń** .
1. Wybierz szablon urządzenia, do którego próbujesz uzyskać wersję.
1. Kliknij przycisk **wersji** w górnej części strony i nadaj szablonowi nową nazwę. Sugerowano nową nazwę, którą można edytować.
1. Kliknij przycisk **Utwórz**.
1. Teraz Twój szablon urządzenia jest w trybie wersji roboczej. Twoje interfejsy są nadal zablokowane i muszą być edytowane osobno. 

### <a name="versioning-an-interface"></a>Przechowywanie wersji interfejsu

Wersja interfejsu pozwala na dodawanie, aktualizowanie i usuwanie funkcji w interfejsie, który został już utworzony. 

Aby uzyskać wersję interfejsu:

1. Przejdź do strony **Szablony urządzeń** .
1. Wybierz szablon urządzenia w trybie wersji roboczej.
1. Wybierz interfejs, który znajduje się w trybie opublikowanym, który chcesz wersji i edytować.
1. Kliknij przycisk **wersji** w górnej części strony interfejsu. 
1. Kliknij przycisk **Utwórz**.
1. Interfejs jest teraz w trybie wersji roboczej. Będziesz mieć możliwość dodawania lub edytowania możliwości do interfejsu bez przerywania istniejących dostosowań i widoków. 

> [!NOTE]
> Nie można wersji ani edytować standardowych interfejsów publikowanych przez usługę Azure IoT. Te standardowe interfejsy są używane do certyfikacji urządzeń.

> [!NOTE]
> Po opublikowaniu interfejsu nie można usunąć jego możliwości nawet w trybie wersji roboczej. Możliwości można edytować lub dodawać tylko do interfejsu w trybie wersji roboczej.


## <a name="migrate-a-device-across-device-template-versions"></a>Migrowanie urządzenia w różnych wersjach szablonów urządzeń

Można utworzyć wiele wersji szablonu urządzenia. Z biegiem czasu będziesz mieć wiele połączonych urządzeń korzystających z tych szablonów urządzeń. Możliwe jest Migrowanie urządzeń z jednej wersji szablonu urządzenia do innej. W poniższych krokach opisano sposób migrowania urządzenia:

1. Przejdź do strony **Device Explorer** .
1. Wybierz urządzenie, które ma być migrowane do innej wersji.
1. Wybierz pozycję **Migruj**.
1. Wybierz szablon urządzenia z numerem wersji, z którym chcesz migrować urządzenie, i wybierz pozycję **Migruj**.

![Jak migrować urządzenie](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, sugerowanym następnym krokiem jest zapoznanie się z informacjami na temat [Azure IoT Edge urządzeń i IoT Central platformy Azure](./concepts-iot-edge.md).

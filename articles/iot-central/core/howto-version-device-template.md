---
title: Opis wersji szablonu urządzenia dla aplikacji Usługi Azure IoT Central | Dokumenty firmy Microsoft
description: Iteruj nad szablonami urządzeń, tworząc nowe wersje i nie wpływając na swoje urządzenia podłączone na żywo
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 37c7bc99881c8d1106c8464cfe18c9e63b8a1b02
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756723"
---
# <a name="create-a-new-device-template-version"></a>Tworzenie nowej wersji szablonu urządzenia

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

Usługa Azure IoT Central umożliwia szybkie tworzenie aplikacji IoT. Możesz szybko iterować projekty szablonów urządzeń, dodając, edytując lub usuwając funkcje urządzenia, widoki i dostosowania. Po opublikowaniu szablonu urządzenia model możliwości urządzenia jest wyświetlany jako **Opublikowany** z ikonami kłód obok modelu. Aby wprowadzić zmiany w modelu możliwości urządzenia, należy utworzyć nową wersję szablonu urządzenia. Tymczasem właściwości chmury, dostosowania i widoki mogą być edytowane w dowolnym momencie bez konieczności wersji szablonu urządzenia. Po zapisaniu którejkolwiek z tych zmian można opublikować szablon urządzenia, aby udostępnić operatorowi najnowsze zmiany do wyświetlenia w Eksploratorze urządzeń.

> [!NOTE]
> Aby dowiedzieć się więcej o tworzeniu szablonu urządzenia, zobacz [Konfigurowanie szablonu urządzenia i zarządzanie nim](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Dodawanie dostosowań do szablonu urządzenia bez przechowywania wersji

Niektóre elementy funkcji urządzenia można edytować bez konieczności wyświetlania wersji szablonu urządzenia i interfejsów. Na przykład niektóre z tych pól obejmują nazwę wyświetlaną, typ semantyczny, wartość minimalną, wartość maksymalną, miejsca dziesiętne, kolor, jednostkę, jednostkę wyświetlania, komentarz i opis. Aby dodać jedno z tych dostosowań:

1. Przejdź do strony **Szablony urządzeń.**
1. Wybierz szablon urządzenia, który chcesz dostosować.
1. Wybierz kartę **Dostosowywanie.**
1. Wszystkie funkcje zdefiniowane w modelu możliwości urządzenia zostaną wymienione tutaj. Wszystkie pola, które można tu edytować, można zapisywać i używać w całej aplikacji, bez konieczności tworzenia wersji szablonu urządzenia. Jeśli istnieją pola, które chcesz edytować, które są tylko do odczytu, należy wersję szablonu urządzenia, aby je zmienić. Wybierz pole, które chcesz edytować, i wprowadź w nowych wartościach.
1. Kliknij pozycję **Zapisz**. Teraz te wartości zastąpią wszystko, co zostało pierwotnie zapisane w szablonie urządzenia i będzie używane w całej aplikacji.

## <a name="versioning-a-device-template"></a>Przechowywanie wersji szablonu urządzenia

Utworzenie nowej wersji szablonu urządzenia spowoduje utworzenie wersji roboczej szablonu, w której można edytować model możliwości urządzenia. Wszystkie opublikowane interfejsy pozostaną opublikowane, dopóki nie zostaną indywidualnie wersjowane. Aby zmodyfikować opublikowany interfejs, należy najpierw utworzyć nową wersję szablonu urządzenia.

Szablon urządzenia powinien być wersjona tylko podczas próby edycji części modelu możliwości urządzenia, której nie można edytować w sekcji dostosowania szablonu urządzenia. 

Aby wersja szablonu urządzenia:

1. Przejdź do strony **Szablony urządzeń.**
1. Wybierz szablon urządzenia, który próbujesz wersją.
1. Kliknij przycisk **Wersja** u góry strony i nadaj szablonowi nową nazwę. Zaproponowaliśmy nową nazwę, którą można edytować.
1. Kliknij przycisk **Utwórz**.
1. Teraz szablon urządzenia jest w trybie roboczym. Zobaczysz, że interfejsy są nadal zablokowane i muszą być indywidualnie wersjowane do edycji. 

### <a name="versioning-an-interface"></a>Przechowywanie wersji interfejsu

Przechowywanie wersji interfejsu umożliwia dodawanie, aktualizowanie i usuwanie możliwości wewnątrz interfejsu, który został już utworzony. 

W celu wersji interfejsu:

1. Przejdź do strony **Szablony urządzeń.**
1. Wybierz szablon urządzenia w trybie roboczym.
1. Wybierz interfejs, który jest w trybie opublikowanym, który chcesz wersji i edytować.
1. Kliknij przycisk **Wersja** u góry strony interfejsu. 
1. Kliknij przycisk **Utwórz**.
1. Teraz interfejs jest w trybie roboczym. Będzie można dodawać lub edytować możliwości do interfejsu bez przerywania istniejących dostosowań i widoków. 

> [!NOTE]
> Standardowe interfejsy opublikowane przez usługę Azure IoT nie mogą być wersjonami ani edytowane. Te standardowe interfejsy są używane do certyfikacji urządzeń.

> [!NOTE]
> Po opublikowaniu interfejsu nie można usunąć żadnych jego możliwości nawet w trybie roboczym. Możliwości można edytować lub dodawać do interfejsu tylko w trybie roboczym.


## <a name="migrate-a-device-across-device-template-versions"></a>Migrowanie urządzenia między wersjami szablonów urządzeń

Można utworzyć wiele wersji szablonu urządzenia. Z biegiem czasu będziesz mieć wiele podłączonych urządzeń przy użyciu tych szablonów urządzeń. Urządzenia można migrować z jednej wersji szablonu urządzenia do innej. W poniższych krokach opisano sposób migracji urządzenia:

1. Przejdź do strony **Eksploratora urządzeń.**
1. Wybierz urządzenie, które chcesz przeprowadzić migrację do innej wersji.
1. Wybierz pozycję **Migruj**.
1. Wybierz szablon urządzenia z numerem wersji, na który chcesz przeprowadzić migrację urządzenia, i wybierz pozycję **Migruj**.

![Jak przeprowadzić migrację urządzenia](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzeń, sugerowanym następnym krokiem jest przeczytanie informacji o [urządzeniach usługi Azure IoT Edge i usłudze Azure IoT Central.](./concepts-iot-edge.md)

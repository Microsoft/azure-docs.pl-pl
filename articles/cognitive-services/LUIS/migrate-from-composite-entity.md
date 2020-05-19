---
title: Uaktualnienie jednostki złożonej — LUIS
description: Uaktualnij jednostkę kompozytową do jednostki uczenia maszynowego z procesem uaktualniania w portalu LUIS.
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 9bbb03a2009bdcb6e2fa5d20aefadd4c7c99f025
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599426"
---
# <a name="upgrade-composite-entity-to-machine-learned-entity"></a>Uaktualnij jednostkę kompozytową do jednostki uczenia maszynowego

Uaktualnij jednostkę kompozytową do jednostki uczenia maszynowego, aby utworzyć jednostkę, która otrzymuje dokładniejsze przewidywania, dzięki czemu można debugować jednostkę.

## <a name="current-version-model-restrictions"></a>Ograniczenia modelu bieżącej wersji

Proces uaktualniania tworzy jednostki znane maszynowo na podstawie istniejących jednostek złożonych w aplikacji w nowej wersji aplikacji. Obejmuje to złożone jednostki podrzędne i role. Proces umożliwia również przełączenie etykiet w przykładowej wyrażenia długości, aby użyć nowej jednostki.

## <a name="upgrade-process"></a>Proces uaktualniania

Proces uaktualniania:
* Tworzy nową jednostkę dodaną maszyną dla każdej jednostki złożonej.
* Jednostki podrzędne:
    * Jeśli jednostka podrzędna jest używana tylko w jednostce złożonej, zostanie dodana tylko do jednostki, którą można wyuczyć.
    * Jeśli jednostka podrzędna jest używana w kompozytie _i_ jako oddzielna jednostka (oznaczona na przykład wyrażenia długości), zostanie ona dodana do wersji jako jednostki i jako podobiektu do nowej jednostki, której dotyczy dana maszyna.
    * Jeśli jednostka podrzędna używa roli, każda rola zostanie przekonwertowana na podjednostkę o tej samej nazwie.
    * Jeśli jednostka podrzędna jest jednostką, która nie jest pouczenia maszynowego (wyrażenie regularne, element listy lub prekompilowana jednostka), zostanie utworzona nowa podjednostka o tej samej nazwie, a nowa podjednostka ma funkcję używającą nieprzygotowanej do użycia jednostki z wymaganą funkcją.
* Nazwy są zachowywane, ale muszą być unikatowe na tym samym poziomie podobiektu/elementu równorzędnego. Zapoznaj się z [unikatowymi limitami nazewnictwa](luis-boundaries.md#name-uniqueness).
* Etykiety w przykładowej wyrażenia długości są przełączane do nowej jednostki udostępnionej przez maszynę z podjednostkami.

Skorzystaj z poniższej tabeli, aby zrozumieć, jak zmienia się model:

|Stary obiekt|Nowy obiekt|Uwagi|
|--|--|--|
|Jednostka złożona|Jednostka poznania maszynowego ze strukturą|Oba obiekty są obiektami nadrzędnymi.|
|Złożona jednostka podrzędna jest **jednostką prostą**|podjednostka|Oba obiekty są obiektami podrzędnymi.|
|Jednostka podrzędna złożona jest **wstępnie zbudowaną jednostką** , taką jak Number|podjednostka o nazwie prekompilowanej jednostki, takiej jak Number i subentity, ma _funkcję_ wbudowanej jednostki numeru z opcją ograniczenia ustawioną na _wartość true_.|podjednostka zawiera funkcję z ograniczeniem na poziomie podjednostki.|
|Jednostka podrzędna złożona jest **wstępnie zbudowaną jednostką** , taką jak Number, a wbudowana jednostka ma **rolę**|podjednostka o nazwie roli i podjednostce ma funkcję wbudowanej jednostki numeru z opcją ograniczenia ustawioną na wartość true.|podjednostka zawiera funkcję z ograniczeniem na poziomie podjednostki.|
|Rola|podjednostka|Nazwa roli jest nazwą podjednostki. Podjednostka jest bezpośrednim elementem podrzędnym jednostki, która jest pouczenia maszynowego.|

## <a name="begin-upgrade-process"></a>Rozpocznij proces uaktualniania

Przed aktualizacją upewnij się, że:

* Zmień wersje, jeśli nie masz odpowiedniej wersji do uaktualnienia


1. Rozpocznij proces uaktualniania od powiadomienia lub możesz poczekać na następny zaplanowany monit.

    > [!div class="mx-imgBorder"]
    > ![Rozpocznij uaktualnianie od powiadomień](./media/update-composite-entity/notification-begin-update.png)

1. W oknie podręcznym wybierz pozycję **Uaktualnij teraz**.

1. Przejrzyj informacje o tym, **co się stanie po uaktualnieniu** informacji, a następnie wybierz pozycję **Kontynuuj**.

1. Wybierz z listy jednostki złożone, które chcesz uaktualnić, a następnie wybierz pozycję **Kontynuuj**.

1. Możesz przenieść wszystkie przeszkolne zmiany z bieżącej wersji do uaktualnionej wersji, zaznaczając pole wyboru.

1. Wybierz pozycję **Kontynuuj** , aby rozpocząć proces uaktualniania.

1. Pasek postępu wskazuje stan procesu uaktualniania.

1. Po zakończeniu procesu nastąpi Nowa przeprowadzona wersja z nowym przeszkolonymi jednostkami. Wybierz pozycję **Wypróbuj nowe jednostki** , aby zobaczyć nową jednostkę.

    Jeśli uaktualnienie lub szkolenie nie powiodło się dla nowej jednostki, powiadomienie zawiera więcej informacji.

1. Na stronie Lista jednostek nowe jednostki są oznaczone symbolem **New** obok nazwy typu.

## <a name="next-steps"></a>Następne kroki

* [Autorzy i współpracownicy](luis-how-to-collaborate.md)
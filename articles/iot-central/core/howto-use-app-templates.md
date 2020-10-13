---
title: Eksportowanie aplikacji IoT Central platformy Azure | Microsoft Docs
description: Jako Menedżer rozwiązań Chcę wyeksportować szablon aplikacji, aby móc go użyć ponownie.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: aec72644f708d6363a80da28c5e571d0165fcdfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651840"
---
# <a name="export-your-application"></a>Eksportowanie aplikacji

W tym artykule opisano, jak Menedżer rozwiązań w celu wyeksportowania aplikacji IoT Central, aby można było użyć jej ponownie.

Dostępne są dwie opcje:

- Kopię aplikacji można utworzyć, jeśli trzeba tylko utworzyć zduplikowaną kopię aplikacji.
- Jeśli planujesz utworzyć wiele kopii, możesz utworzyć szablon aplikacji z aplikacji.

## <a name="copy-your-application"></a>Kopiowanie aplikacji

Można utworzyć kopię dowolnej aplikacji, minus wszystkie wystąpienia urządzeń, historię danych urządzeń i dane użytkowników. Kopia korzysta ze standardowego planu cenowego, w którym zostanie naliczona stawka. Nie można utworzyć aplikacji używającej bezpłatnego planu cenowego, kopiując aplikację.

Wybierz polecenie **Kopiuj**. W oknie dialogowym Wprowadź szczegóły nowej aplikacji. Następnie wybierz pozycję **Kopiuj** , aby potwierdzić, że chcesz kontynuować. Aby dowiedzieć się więcej na temat pól w formularzu, zobacz [Tworzenie aplikacji](quick-deploy-iot-central.md) — Szybki Start.

![Zrzut ekranu przedstawiający stronę ustawień kopiowania aplikacji.](media/howto-use-app-templates/appcopy2.png)

Po pomyślnym zakończeniu operacji kopiowania aplikacji możesz przejść do nowej aplikacji za pomocą linku.

![Strona Ustawienia aplikacji](media/howto-use-app-templates/appcopy3a.png)

Kopiowanie aplikacji powoduje także skopiowanie definicji reguł i akcji poczty e-mail. Niektóre akcje, takie jak Flow i Logic Apps, są powiązane z określonymi regułami za pośrednictwem identyfikatora reguły. Gdy reguła jest kopiowana do innej aplikacji, pobiera swój własny identyfikator reguły. W takim przypadku użytkownicy będą musieli utworzyć nową akcję, a następnie skojarzyć z nią nową regułę. Ogólnie rzecz biorąc, warto sprawdzić reguły i akcje, aby upewnić się, że są one aktualne w nowej aplikacji.

> [!WARNING]
> Jeśli pulpit nawigacyjny zawiera kafelki, które wyświetlają informacje o określonych urządzeniach, wówczas te kafelki pokazują **żądany zasób nie został odnaleziony** w nowej aplikacji. Należy ponownie skonfigurować te kafelki, aby wyświetlić informacje o urządzeniach w nowej aplikacji.

## <a name="create-an-application-template"></a>Tworzenie szablonu aplikacji

Podczas tworzenia aplikacji IoT Central platformy Azure Możesz wybrać wbudowane przykładowe szablony. Możesz również tworzyć własne szablony aplikacji z istniejących aplikacji IoT Central. Przy tworzeniu nowych aplikacji możesz użyć własnych szablonów aplikacji.

Podczas tworzenia szablonu aplikacji zawiera on następujące elementy z istniejącej aplikacji:

- Domyślny pulpit nawigacyjny aplikacji, w tym układ pulpitu nawigacyjnego i wszystkie zdefiniowane kafelki.
- Szablony urządzeń, w tym pomiary, ustawienia, właściwości, polecenia i pulpit nawigacyjny.
- Przepisy. Wszystkie definicje reguł są uwzględniane. Jednak akcje, z wyjątkiem akcji poczty e-mail, nie są uwzględniane.
- Zestawy urządzeń, w tym ich warunki i pulpity nawigacyjne.

> [!WARNING]
> Jeśli pulpit nawigacyjny zawiera kafelki, które wyświetlają informacje o określonych urządzeniach, wówczas te kafelki pokazują **żądany zasób nie został odnaleziony** w nowej aplikacji. Należy ponownie skonfigurować te kafelki, aby wyświetlić informacje o urządzeniach w nowej aplikacji.

Podczas tworzenia szablonu aplikacji nie zawiera on następujących elementów:

- Devices
- Użytkownicy
- Definicje eksportu danych ciągłych

Dodaj te elementy ręcznie do wszystkich aplikacji utworzonych na podstawie szablonu aplikacji.

Aby utworzyć szablon aplikacji na podstawie istniejącej aplikacji IoT Central:

1. Przejdź do sekcji **Administracja** w swojej aplikacji.
1. Wybierz pozycję **Eksportuj szablon aplikacji**.
1. Na stronie **Eksportuj szablon aplikacji** wprowadź nazwę i opis szablonu.
1. Wybierz przycisk **Eksportuj** , aby utworzyć szablon aplikacji. Teraz można skopiować **link** umożliwiający utworzenie nowej aplikacji na podstawie szablonu:

![Tworzenie szablonu aplikacji](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Korzystanie z szablonu aplikacji

Aby użyć szablonu aplikacji do utworzenia nowej aplikacji IoT Central, potrzebujesz wcześniej utworzonego **linku**umożliwiającego udostępnianie. Wklej link umożliwiający **udostępnianie** na pasku adresu przeglądarki. Zostanie wyświetlona strona **Tworzenie aplikacji** z wybranym szablonem aplikacji niestandardowej:

![Tworzenie aplikacji na podstawie szablonu](media/howto-use-app-templates/create-app.png)

Wybierz plan cenowy i wypełnij pozostałe pola w formularzu. Następnie wybierz pozycję **Utwórz** , aby utworzyć nową aplikację IoT Centralową z szablonu aplikacji.

### <a name="manage-application-templates"></a>Zarządzanie szablonami aplikacji

Na stronie **eksport szablonu aplikacji** można usunąć lub zaktualizować szablon aplikacji.

W przypadku usunięcia szablonu aplikacji nie można już używać wcześniej wygenerowanego linku umożliwiającego udostępnianie, aby tworzyć nowe aplikacje.

Aby zaktualizować szablon aplikacji, Zmień nazwę lub opis szablonu na stronie **eksportowania szablonu aplikacji** . Następnie ponownie wybierz przycisk **Eksportuj** . Ta akcja spowoduje wygenerowanie nowego **linku** możliwego do udostępniania i unieważnienie dowolnego adresu URL linku możliwego do **współdzielenia** .

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z szablonów aplikacji, sugerowanym następnym krokiem jest zapoznanie się z tematem [monitorowanie ogólnej kondycji urządzeń podłączonych do aplikacji IoT Central](howto-monitor-application-health.md)

---
title: 'Szybki Start: interakcyjne wyszukiwanie map przy użyciu Azure Maps'
description: 'Szybki Start: Dowiedz się, jak tworzyć interaktywne, z możliwością wyszukiwania mapy. Zobacz Tworzenie konta Azure Maps, uzyskiwanie klucza podstawowego i używanie zestawu SDK sieci Web do konfigurowania aplikacji map'
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: c017ae8044c14a579190f5f1e76cfb1a73e3ce66
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896195"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>Szybki Start: Tworzenie mapy wyszukiwania interaktywnego za pomocą Azure Maps

W tym artykule pokazano, jak za pomocą Azure Maps utworzyć mapę zapewniającą użytkownikom interaktywną obsługę wyszukiwania. Przeprowadza on użytkownika przez następujące podstawowe kroki:

* Tworzenie konta usługi Azure Maps.
* Pobierz klucz podstawowy do użycia w demonstracyjnej aplikacji sieci Web.
* Pobierz i Otwórz aplikację Mapy demonstracyjnej.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Utwórz nowe konto Azure Maps, wykonując następujące czynności:

1. W lewym górnym rogu witryny [Azure Portal](https://portal.azure.com) kliknij przycisk **Utwórz zasób** .
2. W polu *Wyszukaj w witrynie Marketplace* wpisz **Azure Maps** .
3. Z *wyników* wybierz pozycję **Azure Maps** . Kliknij przycisk **Utwórz** znajdujący się poniżej mapy.
4. Na stronie **Tworzenie konta usługi Maps** wprowadź następujące wartości:
    * *Subskrypcja* , która ma być używana dla tego konta.
    * Nazwa *grupy zasobów* dla tego konta. Można wybrać pozycję *Utwórz nowe* lub *Użyj istniejącego* dla grupy zasobów.
    * *Nazwa* nowego konta.
    * *Warstwa cenowa* dla tego konta.
    * Zapoznaj się z *Licencją* oraz *Zasadami zachowania poufności informacji* i zaznacz pole wyboru, aby zaakceptować warunki.
    * Kliknij przycisk **Utwórz** .

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="Tworzenie konta usługi Maps w portalu":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Pobieranie klucza podstawowego konta

Po pomyślnym utworzeniu konta Maps Pobierz klucz podstawowy, który umożliwia wykonywanie zapytań dotyczących interfejsów API map.

1. Otwórz konto usługi Maps w portalu.
2. W sekcji Ustawienia wybierz pozycję **uwierzytelnianie** .
3. Skopiuj **klucz podstawowy** do schowka. Zapisz go lokalnie — będzie używany w dalszej części tego samouczka.

>[!NOTE]
> Jeśli używasz klucza subskrypcji zamiast klucza podstawowego, mapa nie będzie renderowana prawidłowo. Ponadto ze względów bezpieczeństwa zaleca się obracanie między kluczami podstawowymi i pomocniczymi. Aby obrócić klucze, zaktualizuj aplikację tak, aby korzystała z klucza pomocniczego, wdróż, a następnie naciśnij przycisk Cykl/Odśwież obok klucza podstawowego, aby wygenerować nowy klucz podstawowy. Stary klucz podstawowy zostanie wyłączony. Aby uzyskać więcej informacji na temat rotacji kluczy, zobacz [konfigurowanie Azure Key Vault przy użyciu rotacji kluczy i inspekcji](../key-vault/secrets/tutorial-rotation-dual.md)

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Tworzenie konta usługi Maps w portalu":::

## <a name="download-the-demo-application"></a>Pobieranie aplikacji demonstracyjnej

1. Przejdź do [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html). Skopiuj zawartość pliku.
2. Zapisz lokalnie zawartość tego pliku jako **AzureMapDemo.html** . Otwórz ten plik w edytorze tekstów.
3. Wyszukaj ciąg `<Your Azure Maps Key>`. Zastąp go wartością pola **Klucz podstawowy** z poprzedniej sekcji.

## <a name="open-the-demo-application"></a>Otwórz aplikację demonstracyjną

1. Otwórz plik **AzureMapDemo.html** w wybranej przeglądarce.
2. Spójrz na mapę miasta Los Angeles. Powiększ i pomniejsz, aby zobaczyć, jak mapa jest automatycznie renderowana z większą lub mniejszą ilością informacji w zależności od poziomu powiększenia.
3. Zmień domyślny środek mapy. W pliku **AzureMapDemo.html** wyszukaj zmienną o nazwie **center** . Zastąp parę wartości długości i szerokości geograficznej tej zmiennej nowymi wartościami **[-74.0060, 40.7128]** . Zapisz plik i odśwież przeglądarkę.
4. Wypróbuj funkcję wyszukiwania interaktywnego. W polu wyszukiwania w lewym górnym rogu demonstracyjnej aplikacji internetowej wyszukaj **restauracje** .
5. Przesuń wskaźnik myszy na liście adresów i lokalizacji, które są wyświetlane pod polem wyszukiwania. Zwróć uwagę, jak za pomocą odpowiedniej pinezki na mapie wyświetlane są informacje o tej lokalizacji. Ze względu na ochronę danych prywatnych firm wyświetlono fikcyjne nazwy i adresy.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="Tworzenie konta usługi Maps w portalu":::


## <a name="clean-up-resources"></a>Czyszczenie zasobów

>[!WARNING]
>W samouczkach wymienionych w sekcji [następne kroki](#next-steps) szczegółowo opisano sposób używania i konfigurowania Azure Maps przy użyciu konta. Jeśli planujesz kontynuować pracę z samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start.

Jeśli nie planujesz kontynuować korzystania z samouczków, wykonaj następujące kroki, aby wyczyścić zasoby:

1. Zamknij przeglądarkę z uruchomioną aplikacją internetową **AzureMapDemo.html** .
2. Przejdź do strony Azure Portal. Wybierz pozycję **wszystkie zasoby** na stronie portalu głównego. Lub kliknij ikonę menu w lewym górnym rogu. Wybierz pozycję **Wszystkie zasoby** .
3. Kliknij konto Azure Maps. W górnej części strony kliknij pozycję **Usuń** .

Aby uzyskać dodatkowe przykłady kodu i zapoznać się z interaktywnym środowiskiem kodowania, przejrzyj następujące przewodniki:

[Znajdź adres za pomocą usługi Azure Maps Search](how-to-search-for-address.md)

[Użyj kontrolka mapy Azure Maps](how-to-use-map-control.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono konto Azure Maps i utworzono aplikację demonstracyjną. Zapoznaj się z następującymi samouczkami, aby dowiedzieć się więcej na temat Azure Maps:

> [!div class="nextstepaction"]
> [Przeszukaj bliskie punkty zainteresowania przy użyciu Azure Maps](tutorial-search-location.md)
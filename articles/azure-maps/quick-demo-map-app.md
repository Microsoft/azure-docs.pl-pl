---
title: 'Szybki Start: interakcyjne wyszukiwanie map przy użyciu Azure Maps'
description: Dowiedz się, jak utworzyć pokazową aplikację sieci Web dla interakcyjnego wyszukiwania mapy za pomocą Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 5/21/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: da225f9a0bac5d179efadb7d507750c8aa0bc13e
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872102"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Szybki Start: Tworzenie mapy wyszukiwania interaktywnego za pomocą Azure Maps

W tym artykule przedstawiono możliwości usługi Azure Maps w zakresie tworzenia mapy umożliwiającej użytkownikom interaktywne wyszukiwanie. Przeprowadza on użytkownika przez następujące podstawowe kroki:

* Tworzenie konta usługi Azure Maps.
* Pobierz klucz podstawowy do użycia w demonstracyjnej aplikacji sieci Web.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Tworzenie konta przy użyciu usługi Azure Maps

Utwórz nowe konto usługi Maps, wykonując następujące czynności:

1. W lewym górnym rogu witryny [Azure Portal](https://portal.azure.com) kliknij przycisk **Utwórz zasób**.
2. W polu *Wyszukaj w portalu Marketplace* wpisz **Maps**.
3. Z listy *Wyniki* wybierz pozycję **Maps**. Kliknij przycisk **Utwórz** znajdujący się poniżej mapy.
4. Na stronie **Tworzenie konta usługi Maps** wprowadź następujące wartości:
    * *Subskrypcja*, która ma być używana dla tego konta.
    * Nazwa *grupy zasobów* dla tego konta. Można wybrać pozycję *Utwórz nowe* lub *Użyj istniejącego* dla grupy zasobów.
    * *Nazwa* nowego konta.
    * *Warstwa cenowa* dla tego konta.
    * Zapoznaj się z *Licencją* oraz *Zasadami zachowania poufności informacji* i zaznacz pole wyboru, aby zaakceptować warunki.
    * Kliknij przycisk **Utwórz**.

![Tworzenie konta usługi Maps w portalu](./media/quick-demo-map-app/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Pobieranie klucza podstawowego konta

Po pomyślnym utworzeniu konta Maps Pobierz klucz podstawowy, który umożliwia wykonywanie zapytań dotyczących interfejsów API map.

1. Otwórz konto usługi Maps w portalu.
2. W sekcji Ustawienia wybierz pozycję **uwierzytelnianie**.
3. Skopiuj **klucz podstawowy** do schowka. Zapisz go lokalnie — będzie używany w dalszej części tego samouczka.

>[!NOTE]
> Jeśli używasz klucza subskrypcji zamiast klucza podstawowego, mapa nie będzie renderowana prawidłowo. Ponadto ze względów bezpieczeństwa zaleca się obracanie między kluczami podstawowymi i pomocniczymi. Aby obrócić klucze, zaktualizuj aplikację tak, aby korzystała z klucza pomocniczego, wdróż, a następnie naciśnij przycisk Cykl/Odśwież obok klucza podstawowego, aby wygenerować nowy klucz podstawowy. Stary klucz podstawowy zostanie wyłączony. Aby uzyskać więcej informacji na temat rotacji kluczy, zobacz [konfigurowanie Azure Key Vault przy użyciu rotacji kluczy i inspekcji](https://docs.microsoft.com/azure/key-vault/secrets/key-rotation-log-monitoring)

![Pobierz klucz podstawowy Azure Maps w Azure Portal](./media/quick-demo-map-app/get-key.png)

## <a name="download-the-application"></a>Pobieranie aplikacji

1. Przejdź do [interactiveSearch. html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html). Skopiuj zawartość pliku.
2. Zapisz lokalnie zawartość tego pliku jako **AzureMapDemo.html**. Otwórz ten plik w edytorze tekstów.
3. Wyszukaj ciąg `<Your Azure Maps Key>`. Zastąp go wartością pola **Klucz podstawowy** z poprzedniej sekcji.

## <a name="open-the-application"></a>Otwieranie aplikacji

1. Otwórz plik **AzureMapDemo.html** w wybranej przeglądarce.
2. Spójrz na mapę miasta Los Angeles. Powiększ i pomniejsz, aby zobaczyć, jak mapa jest automatycznie renderowana z większą lub mniejszą ilością informacji w zależności od poziomu powiększenia.
3. Zmień domyślny środek mapy. W pliku **AzureMapDemo.html** wyszukaj zmienną o nazwie **center**. Zastąp parę wartości długości i szerokości geograficznej tej zmiennej nowymi wartościami **[-74.0060, 40.7128]**. Zapisz plik i odśwież przeglądarkę.
4. Wypróbuj funkcję wyszukiwania interaktywnego. W polu wyszukiwania w lewym górnym rogu demonstracyjnej aplikacji internetowej wyszukaj **restauracje**.
5. Przesuń wskaźnik myszy na liście adresów i lokalizacji, które są wyświetlane pod polem wyszukiwania. Zwróć uwagę, jak za pomocą odpowiedniej pinezki na mapie wyświetlane są informacje o tej lokalizacji. Ze względu na ochronę danych prywatnych firm wyświetlono fikcyjne nazwy i adresy.

    ![Aplikacja sieci Web do wyszukiwania map interakcyjnych](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tych samouczkach szczegółowo opisano sposób użycia i konfigurowania usługi Azure Maps dla konta. Jeśli planujesz kontynuować pracę z samouczkami, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuowania pracy, wykonaj następujące kroki, aby usunąć zasoby:

1. Zamknij przeglądarkę z uruchomioną aplikacją internetową **AzureMapDemo.html**.
2. W menu po lewej stronie w witrynie Azure Portal wybierz pozycję **Wszystkie zasoby**. Następnie wybierz swoje konto usługi Azure Maps. W górnej części okienka **Wszystkie zasoby** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzyliśmy konto usługi Azure Maps i utworzyliśmy aplikację demonstracyjną. Zapoznaj się z następującymi samouczkami, aby dowiedzieć się więcej na temat Azure Maps:

> [!div class="nextstepaction"]
> [Przeszukaj bliskie punkty zainteresowania przy użyciu Azure Maps](tutorial-search-location.md)

Aby uzyskać dodatkowe przykłady kodu i zapoznać się z interaktywnym środowiskiem kodowania, przejrzyj następujące przewodniki:

> [!div class="nextstepaction"]
> [Znajdź adres za pomocą usługi Azure Maps Search](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Użyj kontrolka mapy Azure Maps](how-to-use-map-control.md)

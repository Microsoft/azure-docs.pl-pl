---
title: 'Samouczek: Zarządzanie flagami funkcji za pomocą usługi Azure App Configuration'
titleSuffix: Azure App Configuration
description: W tym samouczku dowiesz się, jak zarządzać flagami funkcji niezależnie od aplikacji przy użyciu konfiguracji aplikacji platformy Azure.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 0410a1cde12b9ef762d348a286d78b35f7b14bfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932306"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Samouczek: Zarządzanie flagami funkcji w konfiguracji aplikacji platformy Azure

Wszystkie flagi funkcji można przechowywać w konfiguracji aplikacji platformy Azure i administrować nimi z jednego miejsca. Konfiguracja aplikacji ma interfejs użytkownika portalu o nazwie **Menedżer funkcji** , który jest przeznaczony specjalnie dla flag funkcji. Konfiguracja aplikacji również natywnie obsługuje schemat danych flagi funkcji .NET Core.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zdefiniuj flagi funkcji w konfiguracji aplikacji i zarządzaj nimi.
> * Dostęp do flag funkcji z aplikacji.

## <a name="create-feature-flags"></a>Utwórz flagi funkcji

Menedżer funkcji w Azure Portal na potrzeby konfiguracji aplikacji zawiera interfejs użytkownika służący do tworzenia flag funkcji używanych w aplikacjach i zarządzania nimi.

Aby dodać nową flagę funkcji:

1. Wybierz pozycję **Menedżer funkcji**  >  **+ Dodaj** , aby dodać flagę funkcji.

    ![Lista flag funkcji](./media/azure-app-configuration-feature-flags.png)

1. Wprowadź unikatową nazwę klucza dla flagi funkcji. Ta nazwa jest potrzebna do odwoływania się do flagi w kodzie.

1. Jeśli chcesz, podaj opis flagi funkcji.

1. Ustaw stan początkowy flagi funkcji. Ten stan jest zwykle *wyłączony* lub *włączony*. Stan *on* zmienia się na *Conditional* , jeśli dodasz filtr do flagi funkcji.

    ![Tworzenie flagi funkcji](./media/azure-app-configuration-feature-flag-create.png)

1. Gdy stan jest *włączony*, wybierz pozycję **+ Dodaj filtr** , aby określić dodatkowe warunki do zakwalifikowania stanu. Wprowadź wbudowany lub niestandardowy klucz filtru, a następnie wybierz pozycję **+ Dodaj parametr** , aby skojarzyć jeden lub więcej parametrów z filtrem. Filtry wbudowane obejmują:

    | Klucz | Parametry JSON |
    |---|---|
    | Microsoft. PERCENTAGE | {"Value": 0-100 procent} |
    | Microsoft. TimeWindow | {"Start": czas UTC, "koniec": czas UTC} |
    | Microsoft. określania wartości docelowej | {"Odbiorcy": obiekt BLOB JSON definiujący użytkowników, grupy i procenty wdrożenia. Zobacz przykład poniżej `EnabledFor` elementu [tego pliku ustawień](https://github.com/microsoft/FeatureManagement-Dotnet/blob/master/examples/FeatureFlagDemo/appsettings.json) }

    ![Filtr flagi funkcji](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aktualizuj Stany flagi funkcji

Aby zmienić wartość stanu flagi funkcji:

1. Wybierz pozycję **Menedżer funkcji**.

1. Z prawej strony flagi funkcji, którą chcesz zmodyfikować, wybierz wielokropek (**...**), a następnie wybierz pozycję **Edytuj**.

1. Ustaw nowy stan dla flagi funkcji.

## <a name="access-feature-flags"></a>Flagi funkcji dostępu

Flagi funkcji utworzone przez program Feature Manager są przechowywane i pobierane jako zwykłe wartości klucza. Są one przechowywane w ramach specjalnego prefiksu przestrzeni nazw `.appconfig.featureflag` . Aby wyświetlić wartości klucza bazowego, użyj Eksploratora konfiguracji. Aplikacja może pobrać te wartości przy użyciu dostawców konfiguracji aplikacji, zestawów SDK, rozszerzeń wiersza polecenia i interfejsów API REST.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób zarządzania flagami funkcji i ich Stanami przy użyciu konfiguracji aplikacji. Aby uzyskać więcej informacji na temat obsługi zarządzania funkcją w konfiguracji aplikacji i ASP.NET Core, zobacz następujący artykuł:

* [Korzystanie z flag funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md)

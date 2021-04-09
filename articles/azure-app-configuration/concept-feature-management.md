---
title: Informacje o zarządzaniu funkcjami przy użyciu usługi Azure App Configuration
description: Włączanie i wyłączanie funkcji przy użyciu usługi Azure App Configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: cfd676092bcaede58909a3ec1eefeabb4c80f86b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930232"
---
# <a name="feature-management-overview"></a>Omówienie zarządzania funkcjami

Tradycyjnie funkcja dostarczania nowej aplikacji wymaga pełnego ponownego wdrożenia samej aplikacji. Testowanie funkcji często wymaga wielu wdrożeń aplikacji.  Każde wdrożenie może zmienić tę funkcję lub udostępnić funkcję innym klientom do testowania.  

Zarządzanie funkcjami to nowoczesne rozwiązanie do tworzenia oprogramowania, które oddziela wydanie funkcji od wdrożenia kodu i umożliwia szybkie zmiany dostępności funkcji na żądanie. Używa techniki o nazwie *flagi funkcji* (nazywany także *przełącznikami funkcji*, *przełącznikami* funkcji itp.) w celu dynamicznego administrowania cyklem życia funkcji.

Zarządzanie funkcjami ułatwia deweloperom rozwiązywanie następujących problemów:

* **Zarządzanie oddziałami kodu**: Użyj flag funkcji, aby zawijać nowe funkcje aplikacji obecnie opracowywane. Ta funkcja jest domyślnie ukryta. Możesz bezpiecznie dostarczyć funkcję, nawet jeśli nie została ukończona, i pozostanie uśpiona w środowisku produkcyjnym. Korzystając z tego podejścia, nazywanego *ciemnego wdrożenia*, można zwolnić cały kod na końcu każdego cyklu rozwoju. Nie musisz już obsługiwać gałęzi kodu w wielu cyklach programowania, ponieważ dana funkcja wymaga ukończenia więcej niż jednego cyklu.
* **Testowanie w środowisku produkcyjnym**: Użyj flag funkcji, aby udzielić wczesnego dostępu do nowych funkcji w środowisku produkcyjnym. Na przykład można ograniczyć dostęp do członków zespołu lub do wewnętrznych testerów wersji beta. Ci użytkownicy zobaczą środowisko produkcyjne o pełnej wierności, a nie symulowane lub częściowe środowisko w środowisku testowym.
* **Loty**: Użyj flag funkcji, aby stopniowo wdrożyć nowe funkcje dla użytkowników końcowych. Możesz najpierw określić niewielki procent populacji użytkownika i zwiększyć ten procent stopniowo w czasie.
* **Natychmiastowe zabicia przełącznik**: flagi funkcji zapewniają nieodłączną bezpieczeństwo w przypadku zwalniania nowych funkcji. Funkcje aplikacji można włączać i wyłączać bez ponownego wdrażania kodu. W razie potrzeby można szybko wyłączyć funkcję bez konieczności odbudowywania i ponownego wdrażania aplikacji.
* **Aktywacja selektywna**: używanie flag funkcji do segmentacji użytkowników i dostarczania określonego zestawu funkcji dla każdej grupy. Może istnieć funkcja, która działa tylko w niektórych przeglądarkach sieci Web. Można zdefiniować flagę funkcji tak, aby tylko użytkownicy tej przeglądarki mogli zobaczyć i korzystać z tej funkcji. Dzięki temu można łatwo rozwijać obsługiwaną listę przeglądarki w późniejszym czasie bez konieczności wprowadzania zmian w kodzie.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Poniżej przedstawiono kilka nowych warunków związanych z zarządzaniem funkcjami:

* **Flaga funkcji**: Flaga funkcji to zmienna z binarnym stanem *włączania* lub *wyłączania*. Flaga funkcji ma również skojarzony blok kodu. Stan flagi funkcji wyzwala, czy blok kodu jest uruchomiony.
* **Menedżer funkcji**: Menedżer funkcji to pakiet aplikacji, który obsługuje cykl życia wszystkich flag funkcji w aplikacji. Menedżer funkcji oferuje również dodatkowe funkcje, w tym buforowanie flag funkcji i aktualizowanie ich Stanów.
* **Filtr**: filtr jest regułą do oceny stanu flagi funkcji. Potencjalne filtry obejmują grupy użytkowników, urządzenia lub typy przeglądarek, lokalizacje geograficzne i okna czasu.

Skuteczna implementacja zarządzania funkcjami składa się z co najmniej dwóch składników pracujących w ramach współpracy:

* Aplikacja, która korzysta z flag funkcji.
* Oddzielne repozytorium, w którym są przechowywane flagi funkcji i ich bieżące Stany.

## <a name="using-feature-flags-in-your-code"></a>Korzystanie z flag funkcji w kodzie

Wzorzec podstawowy służący do implementowania flag funkcji w aplikacji jest prosty. Flaga funkcji jest zmienną stanu logicznego kontrolującą instrukcję warunkową w kodzie:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Można ustawić wartość `featureFlag` statycznie.

```csharp
bool featureFlag = true;
```

Stan flagi można oszacować na podstawie określonych zasad:

```csharp
bool featureFlag = isBetaUser();
```

Można zwiększyć warunki, aby ustawić zachowanie aplikacji dla dowolnego stanu:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repozytorium flag funkcji

Aby efektywnie korzystać z flag funkcji, należy Externalize wszystkie flagi funkcji używane w aplikacji. Dzięki temu można zmienić Stany flag funkcji bez konieczności modyfikowania i ponownego wdrażania aplikacji.

Konfiguracja aplikacji platformy Azure zapewnia scentralizowane repozytorium dla flag funkcji. Można jej użyć do zdefiniowania różnych rodzajów flag funkcji i manipulowania ich Stanami szybko i bez obaw. Następnie można użyć bibliotek konfiguracyjnych aplikacji dla różnych struktur języka programowania, aby łatwo uzyskać dostęp do tych flag funkcji z aplikacji.

[Użycie flag funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md) pokazuje, w jaki sposób dostawcy konfiguracji aplikacji .NET Core i biblioteki zarządzania funkcjami są używane razem do implementowania flag funkcji dla aplikacji sieci Web ASP.NET.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie flag funkcji do aplikacji sieci Web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  

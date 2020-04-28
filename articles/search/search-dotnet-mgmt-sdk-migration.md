---
title: Uaktualnianie do Azure Search .NET Management SDK wersja 2
titleSuffix: Azure Cognitive Search
description: Uaktualnij do Azure Search .NET Management SDK wersja 2 z poprzednich wersji. Dowiedz się, co nowego i jakie zmiany w kodzie są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73847535"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Uaktualnianie wersji Azure Search .NET Management SDK

> [!Important]
> Ta zawartość jest nadal w fazie tworzenia. Wersja 3,0 zestawu .NET SDK programu Azure Search Management jest dostępna w pakiecie NuGet. Pracujemy nad aktualizacją tego przewodnika migracji, aby wyjaśnić, jak przeprowadzić uaktualnienie do nowej wersji. 
>

W przypadku korzystania z wersji 1.0.2 lub starszej [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 2.

Wersja 2 programu Azure Search .NET Management SDK zawiera pewne zmiany z wcześniejszych wersji. Są one w większości drobnych, więc zmiana kodu powinna wymagać tylko minimalnego nakładu pracy. Zobacz [procedurę uaktualniania](#UpgradeSteps) , aby uzyskać instrukcje dotyczące zmiany kodu w celu użycia nowej wersji zestawu SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Co nowego w wersji 2
Wersja 2 zestawu SDK zarządzania programu Azure Search .NET jest przeznaczona dla tej samej ogólnie dostępnej wersji interfejsu API REST zarządzania Azure Search, jak w poprzednich wersjach zestawu SDK, w odniesieniu do 2015-08-19. Zmiany w zestawie SDK są ścisłymi zmianami po stronie klienta, aby poprawić użyteczność samego zestawu SDK. Są to następujące zmiany:

* `Services.CreateOrUpdate`i jego wersje asynchroniczne teraz automatycznie sonduje Inicjowanie obsługi `SearchService` i nie zwracają do momentu ukończenia aprowizacji usługi. Dzięki temu możesz samodzielnie napisać ten kod sondowania.
* Jeśli nadal chcesz przeprowadzić sondowanie aprowizacji usługi ręcznie, możesz użyć nowej `Services.BeginCreateOrUpdate` metody lub jednej z jej wersji asynchronicznych.
* Do zestawu `Services.Update` SDK dodano nowe metody i jego wersje asynchroniczne. Metody te używają poprawki HTTP do obsługi przyrostowej aktualizacji usługi. Można na przykład skalować usługę przez przekazanie `SearchService` wystąpienia do tych metod, które zawierają tylko żądane `partitionCount` i `replicaCount` właściwości. Stary sposób wywoływania `Services.Get`, modyfikowania zwracanych `SearchService`i przekazywania go do `Services.CreateOrUpdate` jest nadal obsługiwany, ale nie jest już potrzebny. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki do uaktualnienia
Najpierw zaktualizuj odwołanie do `Microsoft.Azure.Management.Search` programu NuGet przy użyciu konsoli Menedżera pakietów NuGet lub kliknij prawym przyciskiem myszy odwołania do projektu i wybierz polecenie "Zarządzaj pakietami NuGet..." w programie Visual Studio.

Po pobraniu przez program NuGet nowych pakietów i ich zależności, należy ponownie skompilować projekt. W zależności od tego, jak kod jest strukturalny, może zostać pomyślnie ponownie odbudowany. Jeśli tak, wszystko jest gotowe!

Jeśli kompilacja nie powiedzie się, może to oznaczać, że wdrożono niektóre interfejsy zestawu SDK (na przykład na potrzeby testowania jednostek), które uległy zmianie. Aby rozwiązać ten problem, należy zaimplementować nowe metody, takie jak `BeginCreateOrUpdateWithHttpMessagesAsync`.

Po naprawieniu błędów kompilacji możesz wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji. Nowe funkcje zestawu SDK są szczegółowo opisane w temacie [co nowego w wersji 2](#WhatsNew).

## <a name="next-steps"></a>Następne kroki
Poznamy Twoją opinię na temat zestawu SDK. Jeśli wystąpią problemy, Opublikuj swoje pytania w [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub zestawu SDK platformy Azure](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest oznaczony przy użyciu "[Search]".

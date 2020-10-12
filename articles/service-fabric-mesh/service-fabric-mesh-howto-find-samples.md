---
title: Znajdź przykłady siatki usługi Azure Service Fabric
description: Oto indeks dostępnych przykładowych aplikacji siatki Service Fabric. Kod źródłowy w tych przykładach pokazuje, jak obsłużyć konkretne scenariusze za pomocą modelu zasobów usługi Service Fabric.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: 64d9e4e112f26f83eedd02b034c8191c6f677618
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261279"
---
# <a name="find-service-fabric-mesh-samples"></a>Znajdowanie przykładów usługi Service Fabric Mesh

W tej tabeli opisano dostępne przykładowe aplikacje usługi Service Fabric Mesh. Kod źródłowy w tych przykładach pokazuje, jak obsłużyć konkretne scenariusze za pomocą modelu zasobów usługi Service Fabric.

Aby uzyskać więcej informacji na temat wdrażania szablonów bezpośrednio na platformie Azure, zobacz [stronę przykładowego szablonu w usłudze GitHub.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Przykładowy szablon|Opis scenariusza|Kod źródłowy|Narzędzia programistyczne|
|------------|--------------------|----------|----------------------|
| [Aplikacja Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Statyczna strona internetowa hostowana w kontenerze. W przypadku systemu Linux jest używany serwer nginx, a w przypadku systemu Windows — usługi IIS | [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Brak wymagań |
| [Aplikacja licznika dla woluminów usługi Azure Files](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Przechowywanie stanu przez zainstalowanie woluminów opartych na usłudze Azure Files wewnątrz kontenera. <br><br> **Uwaga:** ten szablon wymaga, aby udział plików usługi Azure Files był już aprowizowany ([Instrukcje](../storage/files/storage-how-to-create-file-share.md)) | [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Narzędzia usługi Mesh dla programu Visual Studio |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Tworzenie aplikacji z usługą frontonu i zaplecza, która używa rozwiązania opartego na usłudze DNS. Używane jako samouczek [tutaj](./service-fabric-mesh-tutorial-create-dotnetcore.md) | [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Narzędzia usługi Mesh dla programu Visual Studio |
| [Aplikacja obiektów wizualnych](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Skalowanie i uaktualnianie mikrousług w ramach aplikacji. | [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Narzędzia usługi Mesh dla programu Visual Studio |
| [Aplikacja do głosowania](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Tworzenie aplikacji z usługą frontonu i zaplecza, która używa rozwiązania opartego na usłudze DNS | [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Narzędzia siatki programu Visual Studio dla wersji systemu Windows, interfejsu wiersza polecenia VS Code/dotnet mogą być używane w wersji Linux |
| [Aplikacja licznika dla niezawodnych woluminów usługi Service Fabric](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Przechowywanie stanu przez zainstalowanie niezawodnych woluminów dyskowych usługi Service Fabric wewnątrz kontenera.| [Kod źródłowy](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Narzędzia usługi Mesh dla programu Visual Studio |

---
title: Znajdź przykłady siatki usługi Azure Service Fabric
description: Oto indeks dostępnych przykładowych aplikacji siatki Service Fabric. Kod źródłowy w tych przykładach pokazuje, jak obsłużyć konkretne scenariusze za pomocą modelu zasobów usługi Service Fabric.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: 2b38cd6a6c0f3aaab4ff5b77be82aee242afef23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627032"
---
# <a name="find-service-fabric-mesh-samples"></a>Znajdowanie przykładów usługi Service Fabric Mesh

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

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

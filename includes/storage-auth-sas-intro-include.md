---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75371784"
---
Sygnatura dostępu współdzielonego umożliwia przyznanie ograniczonego dostępu do kontenerów i obiektów BLOB na koncie magazynu. Podczas tworzenia sygnatury dostępu współdzielonego należy określić jego ograniczenia, w tym zasoby usługi Azure Storage, do których klient może uzyskać dostęp, jakie mają uprawnienia do tych zasobów oraz czas ważności sygnatury dostępu współdzielonego.

Każde sygnatura dostępu współdzielonego jest podpisana przy użyciu klucza. Sygnaturę dostępu współdzielonego można podpisać na jeden z dwóch sposobów:

- Z kluczem utworzonym przy użyciu poświadczeń Azure Active Directory (Azure AD). Sygnatura dostępu współdzielonego z poświadczeniami usługi Azure AD to sygnatura dostępu współdzielonego *delegowania użytkownika* .
- Za pomocą klucza konta magazynu. *Sygnatura dostępu współdzielonego usługi* i *sygnatura dostępu współdzielonego konta* są podpisane przy użyciu klucza konta magazynu.

Sygnatura dostępu współdzielonego w ramach delegowania użytkowników zapewnia wyższy dostęp do sygnatury SAS podpisanej przy użyciu klucza konta magazynu. Jeśli to możliwe, firma Microsoft zaleca używanie sygnatury dostępu współdzielonego. Aby uzyskać więcej informacji, zobacz [udzielanie ograniczonego dostępu do danych za pomocą sygnatur dostępu współdzielonego (SAS)](../articles/storage/common/storage-sas-overview.md).

---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011742"
---
Wszystkie dane przechowywane w Azure Files są szyfrowane przy użyciu funkcji szyfrowania usługi Azure Storage (SSE). Szyfrowanie usługi Storage działa podobnie do funkcji BitLocker w systemie Windows: dane są szyfrowane poniżej poziomu systemu plików. Ponieważ dane są szyfrowane pod systemem plików udziału plików platformy Azure, ponieważ są one zakodowane na dysk, nie trzeba mieć dostępu do klucza bazowego na kliencie w celu odczytu lub zapisu w udziale plików platformy Azure. Szyfrowanie w spoczynku dotyczy protokołów SMB i NFS.

Domyślnie dane przechowywane w Azure Files są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. W przypadku kluczy zarządzanych przez firmę Microsoft Firma Microsoft przechowuje klucze służące do szyfrowania/odszyfrowywania danych i jest odpowiedzialna za ich regularne obracanie. Możesz również zarządzać własnymi kluczami, co zapewnia kontrolę nad procesem obrotu. Jeśli zdecydujesz się na zaszyfrowanie udziałów plików za pomocą kluczy zarządzanych przez klienta, Azure Files będzie autoryzowany do uzyskiwania dostępu do kluczy w celu spełnienia żądań odczytu i zapisu od klientów. W przypadku kluczy zarządzanych przez klienta można odwołać tę autoryzację w dowolnym momencie, ale oznacza to, że udział plików platformy Azure nie będzie już dostępny za pośrednictwem protokołu SMB lub interfejsu API FileREST.

Azure Files używa tego samego schematu szyfrowania co inne usługi Azure Storage, takie jak Azure Blob Storage. Aby dowiedzieć się więcej na temat szyfrowania usługi Azure Storage (SSE), zobacz Szyfrowanie w usłudze [Azure Storage dla danych przechowywanych w stanie spoczynku](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
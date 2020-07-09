---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597865"
---
Wszystkie dane przechowywane w Azure Files są szyfrowane przy użyciu funkcji szyfrowania usługi Azure Storage (SSE). Szyfrowanie usługi Storage działa podobnie do funkcji BitLocker w systemie Windows: dane są szyfrowane poniżej poziomu systemu plików. Ponieważ dane są szyfrowane pod systemem plików udziału plików platformy Azure, ponieważ są one zakodowane na dysk, nie trzeba mieć dostępu do klucza bazowego na kliencie w celu odczytu lub zapisu w udziale plików platformy Azure.

Domyślnie dane przechowywane w Azure Files są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. W przypadku kluczy zarządzanych przez firmę Microsoft Firma Microsoft przechowuje klucze służące do szyfrowania/odszyfrowywania danych i jest odpowiedzialna za ich regularne obracanie. Możesz również zarządzać własnymi kluczami, co zapewnia kontrolę nad procesem obrotu. Jeśli zdecydujesz się na zaszyfrowanie udziałów plików za pomocą kluczy zarządzanych przez klienta, Azure Files będzie autoryzowany do uzyskiwania dostępu do kluczy w celu spełnienia żądań odczytu i zapisu od klientów. W przypadku kluczy zarządzanych przez klienta można odwołać tę autoryzację w dowolnym momencie, ale oznacza to, że udział plików platformy Azure nie będzie już dostępny za pośrednictwem protokołu SMB lub interfejsu API FileREST.

Azure Files używa tego samego schematu szyfrowania co inne usługi Azure Storage, takie jak Azure Blob Storage. Aby dowiedzieć się więcej na temat szyfrowania usługi Azure Storage (SSE), zobacz Szyfrowanie w usłudze [Azure Storage dla danych przechowywanych w stanie spoczynku](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
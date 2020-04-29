---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77157226"
---
Opcje nadmiarowości dla konta magazynu obejmują:

* Magazyn lokalnie nadmiarowy (LRS): prosta, tania strategia nadmiarowości. Dane są kopiowane synchronicznie trzykrotnie w regionie podstawowym.
* Magazyn strefowo nadmiarowy (ZRS): nadmiarowość dla scenariuszy wymagających wysokiej dostępności. Dane są kopiowane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym.
* Magazyn Geograficznie nadmiarowy (GRS): wieloregionalna nadmiarowość do ochrony przed awarią regionalną. Dane są kopiowane synchronicznie do regionu podstawowego, a następnie kopiowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu do danych w regionie pomocniczym, Włącz magazyn Geograficznie nadmiarowy z dostępem do odczytu (RA-GRS).
* Magazyn Geograficznie nadmiarowy (GZRS) (wersja zapoznawcza): nadmiarowość dla scenariuszy wymagających zarówno wysokiej dostępności, jak i maksymalnej trwałości. Dane są kopiowane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym, a następnie kopiowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu do danych w regionie pomocniczym, Włącz strefę geograficzną z dostępem do odczytu (RA-GZRS).

Aby uzyskać więcej informacji na temat opcji nadmiarowości w usłudze Azure Storage, zobacz [nadmiarowość usługi Azure Storage](../articles/storage/common/storage-redundancy.md).

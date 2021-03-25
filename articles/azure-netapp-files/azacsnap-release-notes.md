---
title: Informacje o wersji dla narzędzia migawek spójnej na poziomie aplikacji platformy Azure dla Azure NetApp Files | Microsoft Docs
description: Zawiera informacje o wersji narzędzia migawek spójnych w aplikacji Azure, których można używać z Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111448"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Informacje o wersji narzędzia do tworzenia migawek spójnych w aplikacji Azure (wersja zapoznawcza)

Ta strona zawiera listę najważniejszych zmian wprowadzonych w programie AzAcSnap w celu zapewnienia nowych funkcji lub rozwiązywania wad.

## <a name="march-2021"></a>Marzec – 2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>Wersja zapoznawcza AzAcSnap v 5.0 (kompilacja: 20210318.30771)

AzAcSnap v 5.0 wersja zapoznawcza (kompilacja: 20210318.30771) została wydana z następującymi poprawkami i ulepszeniami:

- Usunięto konieczność dodania użytkownika AZACSNAP do baz danych dzierżawy SAP HANA, zobacz sekcję [Włączanie komunikacji z SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana) .
- Popraw, aby zezwolić na [przywracanie](azacsnap-cmd-ref-restore.md) z woluminami skonfigurowanymi przy użyciu ręcznej jakości usług.
- Dodano formant mutex w celu ograniczenia połączeń SSH dla dużego wystąpienia platformy Azure.
- Napraw Instalatora w celu obsługi nazw ścieżek ze spacjami i innymi powiązanymi problemami.
- W przypadku przygotowania do obsługi innych serwerów baz danych zmieniono opcjonalny parametr "--hanasid" na "--dbsid".

Pobierz [najnowszą wersję](https://aka.ms/azacsnapdownload) Instalatora i zapoznaj się z tematem jak [zacząć pracę](azacsnap-get-started.md).

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do narzędzia migawek spójnych z aplikacją platformy Azure](azacsnap-get-started.md)

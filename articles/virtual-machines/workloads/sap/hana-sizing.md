---
title: Ustalanie wielkości SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Ustalanie wielkości SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d49191abbba9c189672be4cd8bad4346e9689bf6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675426"
---
# <a name="sizing"></a>Ustalanie rozmiaru

Ustalanie wielkości dla dużych wystąpień platformy HANA nie jest inne niż w ogólnym rozmiarze dla platformy HANA. W przypadku istniejących i wdrożonych systemów, które chcesz przenieść z innych RDBMS do platformy HANA, oprogramowanie SAP udostępnia wiele raportów, które są uruchamiane w istniejących systemach SAP. Jeśli baza danych jest przenoszona do platformy HANA, te raporty sprawdzają dane i obliczają wymagania dotyczące pamięci dla wystąpienia platformy HANA. Aby uzyskać więcej informacji na temat sposobu uruchamiania tych raportów i uzyskiwania najnowszych poprawek lub wersji, przeczytaj następujące informacje dotyczące oprogramowania SAP:

- [Uwagi dotyczące oprogramowania SAP #1793345 — ustalanie wielkości dla pakietu SAP Suite na platformie HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Test SAP #1872170 — pakiet w witrynie HANA i S/4 — raport o wymiarach](https://launchpad.support.sap.com/#/notes/1872170)
- [Uwaga dotycząca oprogramowania SAP #2121330 — często zadawane pytania: SAP BW w raporcie dotyczącym określania wielkości platformy HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [Uwaga dotycząca oprogramowania SAP #1736976 — raport o ustalaniu wielkości dla wagi na platformie HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [Uwagi dotyczące oprogramowania SAP #2296290 — nowy raport o ustalaniu wielkości dla wagi na platformie HANA](https://launchpad.support.sap.com/#/notes/2296290)

W przypadku implementacji zielonych pól dostęp do usługi SAP Quick size jest dostępny w celu obliczenia wymagań dotyczących pamięci implementacji oprogramowania SAP na platformie HANA.

Wymagania dotyczące pamięci dla programu HANA zwiększają się wraz ze wzrostem ilości danych. Zapoznaj się z bieżącym użyciem pamięci, aby pomóc w przewidywaniu, co będzie w przyszłości. W oparciu o wymagania dotyczące pamięci można następnie zmapować zapotrzebowanie na jedną z jednostek SKU dużego wystąpienia platformy HANA.

**Następne kroki**
- Zapoznaj się z [wymaganiami dotyczącymi](hana-onboarding-requirements.md) dołączania
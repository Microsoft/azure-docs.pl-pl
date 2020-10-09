---
title: Warstwy danych i węzły rozszerzeń dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Warstwy danych i węzły rozszerzeń dla SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3553ac9704ac26d0bdaae0f93b89f41a87ac716
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77617161"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Używanie SAP HANA warstw i węzłów rozszerzeń

System SAP obsługuje model warstwy danych dla SAP BW różnych wydań SAP NetWeaver i SAP BW/4HANA. Aby uzyskać więcej informacji na temat modelu warstwowego danych, zobacz dokument SAP [SAP BW/4HANA i SAP BW na platformie Hana z węzłami rozszerzenia SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
W przypadku dużego wystąpienia programu HANA można użyć konfiguracji opcji-1 SAP HANA węzłów rozszerzeń, jak wyjaśniono w dokumentach w blogu dotyczącej często zadawanych pytań i oprogramowania SAP. Konfigurację opcji 2 można skonfigurować przy użyciu następujących jednostek SKU dużych wystąpień platformy HANA: S72m, S192, S192m, S384 i S384m. 

Po przeszukaniu dokumentacji korzyść może nie być widoczna natychmiast. Jednak podczas przeglądania wytycznych dotyczących ustalania rozmiarów SAP można zobaczyć zalety, używając opcji-1 i opcji-2 SAP HANA węzłów rozszerzenia. Oto przykłady:

- Wskazówki dotyczące ustalania rozmiaru SAP HANA zwykle wymagają podwójnej ilości danych w postaci ilości pamięci. Po uruchomieniu wystąpienia SAP HANA z gorącą ilością danych masz tylko 50% lub mniej pamięci wypełnionej danymi. Pozostała część pamięci jest optymalna do SAP HANA wykonywania zadań.
- Oznacza to, że w jednostce S192 z dużym wystąpieniem platformy HANA istnieje 2 TB pamięci, działająca baza danych SAP BW, masz tylko 1 TB jako ilość danych.
- Jeśli używasz dodatkowego węzła rozszerzenia SAP HANA opcji-1, również jednostki SKU dużego wystąpienia S192 HANA, zapewnia ona dodatkową pojemność 2 TB dla ilości danych. W konfiguracji opcji 2 można uzyskać dodatkowe 4 TB dla woluminu danych ciepłej. W porównaniu z węzłem gorącą pełna pojemność pamięci w węźle rozszerzenia "gorąca" może być używana do przechowywania danych dla opcji-1. Podwójna pamięć może być używana w przypadku ilości danych w opcji 2 SAP HANA konfiguracja węzła rozszerzenia.
- W przypadku danych i współczynnika gorąca do ciepłego 1:2 dla opcji-1 zostanie osiągnięta pojemność 3 TB. Masz 5 TB danych i współczynnik 1:4 z konfiguracją węzła rozszerzenia opcji 2.

Im większy wolumin danych w porównaniu z pamięcią, tym większe prawdopodobieństwo, że dane ciepłe są przechowywane w magazynie dyskowym.

**Następne kroki**
- Rozpoznaj [architekturę SAP HANA (duże wystąpienia) na platformie Azure](hana-architecture.md)
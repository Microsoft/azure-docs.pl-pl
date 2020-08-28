---
title: 'Odwołanie: Data Science Virtual Machine wycofania obrazów'
titleSuffix: Azure Data Science Virtual Machine
description: Szczegóły dotyczące emerytur, które mają wpływ na Data Science Virtual Machine platformy Azure
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: d5f541dec14eebc944e4eac11dbe569b38cb277e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001624"
---
# <a name="reference-retirements-of-dsvm-images"></a>Dokumentacja: wycofania obrazów DSVM

Poniżej omówiono wycofanie (wycofanie) obrazów oraz sugestie dotyczące przejmowania nadchodzących emerytur na platformie Azure Data Science Virtual Machine.

## <a name="why-we-retire-dsvm-images"></a>Dlaczego wycofamy obrazy DSVM

Obecnie Data Science Virtual Machine ma dwie wersje:

* Ubuntu
* Windows Server

Obraz DSVM dla tych wersji jest oparty na określonej wersji systemu operacyjnego, na przykład Ubuntu 18,04 LTS. Z upływem czasu okno obsługi _wersji_ systemu operacyjnego zakończy się i/lub narzędzia do nauki o danych nie będą już obsługiwać starszych wersji systemu operacyjnego. Aby zapewnić aktualność obrazu DSVM przy użyciu najnowszych systemów operacyjnych i narzędzi do nauki o danych, firma Microsoft udostępnia nowy obraz DSVM na podstawie nowszych wersji systemu operacyjnego.

## <a name="how-we-retire-dsvm-images"></a>Jak wycofać obrazy DSVM

Firma Microsoft udostępnia _ogłoszenie dotyczące emerytury_ , w przypadku których wszyscy użytkownicy DSVM są powiadamiani (za pośrednictwem poczty e-mail) nadchodzącego wycofania z obrazu DSVM. W ogłoszeniu dotyczącym emerytury zostanie szczegółowo podany _dzień wycofania_ (po tej dacie nie jest dostępny obraz) i zaleceń zaradczych (na przykład uaktualnienie do nowszego obrazu DSVM).

Na stronie _anonsu_ zostanie ukryty obraz w portalu Marketplace, dzięki czemu:

1. Nowi użytkownicy nie mogą przypadkowo zainicjować obsługi obrazu DSVM.
2. istniejący użytkownicy mogą używać wdrożeń ARM do daty wycofania.

Obraz ukryty otrzyma poprawki systemu operacyjnego do momentu zakończenia _okresu wycofania_ , ale __nie__ będzie otrzymywać aktualizacji do narzędzi i struktur analizy danych. W _dniu wycofania_obraz nie będzie dostępny dla wdrożeń usługi ARM.

Każdy zainicjowany obraz DSVM w subskrypcji będzie nadal działać po dacie wycofania. Zalecamy jednak uaktualnienie do najnowszego obrazu DSVM, dzięki czemu masz najnowsze systemy operacyjne i narzędzia do nauki o danych.

## <a name="impact"></a>Wpływ

Istniejące DSVM zainicjowane obrazy w subskrypcji będą nadal działać po dacie wycofania. Zaleca się jednak, aby użytkownicy uaktualnili swój obraz DSVM do nowszej wersji przy użyciu szablonu Azure Portal lub ARM.

> [!WARNING]
> Wycofane obrazy DSVM zainicjowane przy użyciu Virtual Machine Scale Sets nie będą skalowane w górę po dacie wycofania.
>
> Szablony ARM, które nie zostały zaktualizowane przy użyciu nowych szczegółów obrazu DSVM, zakończą się niepowodzeniem po dacie wycofania.


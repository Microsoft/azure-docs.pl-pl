---
title: plik dołączany
description: plik dołączany
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183201"
---
Możesz otworzyć port lub utworzyć punkt końcowy na maszynie wirtualnej na platformie Azure, tworząc filtr sieci dla podsieci lub interfejsu sieciowego maszyny wirtualnej. Należy umieścić te filtry, które kontrolują ruch przychodzący i wychodzący, w sieciowej grupie zabezpieczeń dołączonej do zasobu, który odbiera ruch.

W przykładzie w tym artykule pokazano, jak utworzyć filtr sieci korzystający ze standardowego portu TCP 80 (zakłada się, że już uruchomiono odpowiednie usługi i otwarto wszystkie reguły zapory systemu operacyjnego na maszynie wirtualnej).

Po utworzeniu maszyny wirtualnej skonfigurowanej do obsłużenia żądań sieci Web na standardowym porcie TCP 80 można:

1. Utworzenie sieciowej grupy zabezpieczeń.

2. Utwórz regułę zabezpieczeń dla ruchu przychodzącego zezwalającą na ruch i przypisując wartości do następujących ustawień:

   - **Docelowe zakresy portów**: 80

   - **Zakresy portów źródłowych**: * (zezwala na dowolny port źródłowy)

   - **Wartość priorytetu**: wprowadź wartość o wartości mniejszej niż 65 500 i wyższy priorytet niż domyślna reguła "Odrzuć wszystkie odmowy".

3. Skojarz grupę zabezpieczeń sieci z interfejsem sieciowym lub podsiecią maszyny wirtualnej.

Chociaż w tym przykładzie użyto prostej reguły do zezwalania na ruch HTTP, można także użyć sieciowych grup zabezpieczeń i reguł, aby utworzyć bardziej złożone konfiguracje sieci. 





---
title: Tworzenie grup adresów IP w zaporze platformy Azure
description: Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444539"
---
# <a name="create-ip-groups-preview"></a>Tworzenie grup adresów IP (wersja zapoznawcza)

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi. Mogą mieć jeden adres IP, wiele adresów IP lub jeden lub więcej zakresów adresów IP.

## <a name="create-an-ip-group"></a>Tworzenie grupy adresów IP

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym Wyszukaj wpisz ciąg **grupy IP** , a następnie wybierz pozycję **grupy adresów IP**.
3. Wybierz przycisk **Utwórz**.
4. Wybierz subskrypcję.
5. Wybierz grupę zasobów lub Utwórz nową.
6. Wpisz unikatową nazwę grupy adresów IP, a następnie wybierz region.

6. Wybierz pozycję **Dalej: adresy IP**.
7. Wpisz adres IP, wiele adresów IP lub zakresy adresów IP.

   Istnieją dwa sposoby wprowadzania adresów IP:
   - Możesz wprowadzić je ręcznie
   - Możesz zaimportować je z pliku

   Aby zaimportować plik z pliku, wybierz pozycję **Importuj z pliku**. Możesz przeciągnąć plik do pola lub wybrać pozycję **Przeglądaj w poszukiwaniu plików**. W razie potrzeby można przeglądać i edytować przekazane adresy IP.

   Po wpisaniu adresu IP Portal sprawdza jego poprawność, aby sprawdzić, czy występują problemy z nakładającymi się, duplikowaniem i formatowaniem.

5. Po zakończeniu wybierz pozycję **Przegląd + Utwórz**.
6. Wybierz przycisk **Utwórz**.


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej na temat grup adresów IP](ip-groups.md)
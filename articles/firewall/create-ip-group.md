---
title: Tworzenie grup adresów IP w zaporze platformy Azure
description: Grupy IP umożliwiają grupowanie adresów IP dla reguł zapory platformy Azure i zarządzanie nimi.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85602537"
---
# <a name="create-ip-groups"></a>Tworzenie grup adresów IP

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
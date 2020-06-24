---
title: Resetowanie haseł dla maszyn wirtualnych laboratorium z klasy Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak resetować hasła do maszyn wirtualnych w laboratoriach Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: f5b783b6c6afd634b7539326678d1caf91d3ad0c
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895836"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Ustawianie lub Resetowanie hasła dla maszyn wirtualnych w laboratoriach z zajęć (studenci)
W tym artykule opisano, jak uczniowie mogą ustawiać/resetować hasła dla swoich maszyn wirtualnych. 

## <a name="enable-resetting-of-passwords"></a>Włącz resetowanie haseł
W momencie tworzenia laboratorium właściciel laboratorium może włączyć lub wyłączyć **Korzystanie z tego samego hasła dla wszystkich maszyn wirtualnych**. Jeśli ta opcja została włączona, uczniowie nie mogą resetować hasła. Wszystkie maszyny wirtualne w laboratoriach będą miały takie samo hasło, które zostało ustawione przez instruktora. 

Jeśli ta opcja jest wyłączona, użytkownicy będą musieli ustawić hasło podczas próby nawiązania połączenia z maszyną wirtualną po raz pierwszy. Studenci mogą również zresetować hasło później, jak pokazano w ostatniej sekcji tego artykułu. 

## <a name="reset-password-for-the-first-time"></a>Resetuj hasło po raz pierwszy
Jeśli opcja **Użyj tego samego hasła dla wszystkich maszyn wirtualnych** została wyłączona, gdy użytkownicy (studenci) wybierzą przycisk **Połącz** na kafelku Lab na stronie **moje maszyny wirtualne** , użytkownik zobaczy następujące okno dialogowe, aby ustawić hasło dla maszyny wirtualnej: 

![Zresetuj hasło dla ucznia](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Zresetuj hasło później
Student może również ustawić hasło, klikając menu przepełnienie (w**pionie, trzy kropki**) na kafelku Lab i wybierając pozycję **Resetuj hasło**. 

![Zresetuj hasło później](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat opcji użycia uczniów, które może skonfigurować właściciel laboratorium, zobacz następujący artykuł: [Konfigurowanie użycia ucznia](how-to-configure-student-usage.md).

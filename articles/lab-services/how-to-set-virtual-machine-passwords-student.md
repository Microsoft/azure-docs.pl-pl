---
title: Resetowanie haseł dla maszyn wirtualnych laboratorium z klasy Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak resetować hasła do maszyn wirtualnych w laboratoriach Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1b0b13862ca4620da15606138c0a80adeac8056a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436814"
---
# <a name="set-or-reset-password-for-virtual-machines-in-labs-students"></a>Ustawianie lub Resetowanie hasła dla maszyn wirtualnych w laboratoriach (studentów)
W tym artykule opisano, jak uczniowie mogą ustawiać/resetować hasła dla swoich maszyn wirtualnych. 

## <a name="enable-resetting-of-passwords"></a>Włącz resetowanie haseł
W momencie tworzenia laboratorium właściciel laboratorium może włączyć lub wyłączyć **Korzystanie z tego samego hasła dla wszystkich maszyn wirtualnych**. Jeśli ta opcja została włączona, uczniowie nie mogą resetować hasła. Wszystkie maszyny wirtualne w laboratoriach będą miały takie samo hasło, które zostało ustawione przez instruktora. 

Jeśli ta opcja jest wyłączona, użytkownicy będą musieli ustawić hasło podczas próby nawiązania połączenia z maszyną wirtualną po raz pierwszy. Studenci mogą również zresetować hasło później, jak pokazano w ostatniej sekcji tego artykułu. 

## <a name="reset-password-for-the-first-time"></a>Resetuj hasło po raz pierwszy
Jeśli opcja **Użyj tego samego hasła dla wszystkich maszyn wirtualnych** została wyłączona, gdy użytkownicy (studenci) wybierzą przycisk **Połącz** na kafelku Lab na stronie **moje maszyny wirtualne** , użytkownik zobaczy następujące okno dialogowe, aby ustawić hasło dla maszyny wirtualnej: 

![Zresetuj hasło dla ucznia](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Zresetuj hasło później
Student może również ustawić hasło, klikając menu przepełnienie (w **pionie, trzy kropki**) na kafelku Lab i wybierając pozycję **Resetuj hasło**. 

![Zresetuj hasło później](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat opcji użycia uczniów, które może skonfigurować właściciel laboratorium, zobacz następujący artykuł: [Konfigurowanie użycia ucznia](how-to-configure-student-usage.md).

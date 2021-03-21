---
title: Dodawanie adresatów w udziale danych platformy Azure
description: Dowiedz się, jak dodać odbiorców do istniejącego udziału danych w udziale danych platformy Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680637"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>Jak dodać odbiorcę do udziału

Możesz dodać adresata podczas tworzenia nowego udziału lub istniejącego udziału. Z poziomu interfejsu użytkownika usługi Azure Data Share możesz dodać adresata przy użyciu adresu e-mail logowania użytkownika platformy Azure.  Z poziomu interfejsu API można użyć kombinacji nazwy głównej użytkownika/usługi i identyfikatora dzierżawy. W przypadku określenia identyfikatora dzierżawy zaproszenie może zostać zaakceptowane tylko do tej dzierżawy. Ponadto z interfejsu API można utworzyć zaproszenie bez wysyłania wiadomości e-mail do adresata. 

## <a name="add-recipient-to-an-existing-share"></a>Dodaj odbiorcę do istniejącego udziału

W udziale danych platformy Azure przejdź do wysłanego udziału i wybierz kartę **zaproszenia** . W tym miejscu znajdują się wszyscy adresaci zaproszeń do tego udziału danych. Aby dodać nowe konto, kliknij przycisk **Dodaj adresata**.

![Zrzut ekranu przedstawia wybraną dodanie odbiorcy.](./media/how-to/how-to-add-recipients/add-recipient.png)

Po prawej stronie zostanie wyświetlony panel. Kliknij pozycję **Dodaj odbiorcę** , a następnie wprowadź adres e-mail nowego odbiorcy w pustym wierszu. Upewnij się, że używasz poczty e-mail do logowania na platformie Azure odbiorcy (użycie ich aliasu poczty e-mail nie będzie działało). 

![Zrzut ekranu przedstawia okienko Dodaj odbiorcę, w którym można dodawać i wysyłać zaproszenia.](./media/how-to/how-to-add-recipients/add-recipient-side.png)

Kliknij przycisk **Dodaj i Wyślij zaproszenie**. Nowi adresaci będą wysyłać wiadomości e-mail z zaproszeniem do tego udziału.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [usuwania zaproszenia do udziału](how-to-delete-invitation.md).

---
title: Integrowanie pulpitu wirtualnego systemu Windows z Azure Advisor — Azure
description: Jak używać Azure Advisor z wdrożeniem pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b141d3829e9d98cb793d2038e93331804d2e6b8f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448291"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Używanie Azure Advisor z pulpitem wirtualnym systemu Windows

Azure Advisor może pomóc użytkownikom w rozwiązywaniu typowych problemów bez konieczności obsługi plików. Zalecenia zmniejszają konieczność przesyłania wniosków pomocy, oszczędzania czasu i kosztów.

W tym artykule przedstawiono sposób konfigurowania Azure Advisor w ramach wdrożenia pulpitu wirtualnego systemu Windows w celu ułatwienia użytkownikom.

## <a name="what-is-azure-advisor"></a>Co to jest Azure Advisor?

Azure Advisor analizuje konfiguracje i dane telemetryczne w celu zaoferowania spersonalizowanych zaleceń w celu rozwiązywania typowych problemów. Korzystając z tych zaleceń, można zoptymalizować zasoby platformy Azure pod kątem niezawodności, bezpieczeństwa, doskonałości operacyjnej, wydajności i kosztów. Dowiedz się więcej o [witrynie sieci web Azure Advisor](https://azure.microsoft.com/services/advisor/).

## <a name="how-to-start-using-azure-advisor"></a>Jak rozpocząć korzystanie z Azure Advisor

Wszystko, co musisz zrobić, to konto platformy Azure na Azure Portal. Najpierw otwórz Azure Portal w <https://portal.azure.com/#home> , a następnie wybierz pozycję **Advisor** w obszarze **usługi platformy Azure**, jak pokazano na poniższej ilustracji. Możesz również wprowadzić ciąg "Azure Advisor" na pasku wyszukiwania w Azure Portal.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający Azure Portal. Użytkownik kursoruje wskaźnik myszy nad łączem Azure Advisor, co powoduje wyświetlenie menu rozwijanego.](media/azure-advisor.png)

Po otwarciu Azure Advisor zobaczysz pięć kategorii:

- Koszt
- Zabezpieczenia
- Niezawodność
- Doskonałość operacyjna
- Wydajność

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający Azure Advisor pokazujący pięć menu dla każdej kategorii. Pięć elementów wyświetlanych w ich własnych polach jest kosztem, bezpieczeństwem, niezawodnością, doskonałością operacyjną i wydajnością.](media/advisor-categories.png)

Po wybraniu kategorii nastąpi przejście do strony aktywne zalecenia. Na tej stronie możesz zobaczyć, które rekomendacje Azure Advisor dla Ciebie, jak pokazano na poniższej ilustracji.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający listę aktywnych rekomendacji dla doskonałości operacyjnej. Lista pokazuje siedem zaleceń z różnymi poziomami priorytetów.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Dodatkowe porady dotyczące Azure Advisor

- Upewnij się, że często sprawdzasz zalecenia, co najmniej raz w tygodniu. Azure Advisor aktualizuje aktywne zalecenia wiele razy dziennie. Sprawdzanie pod kątem nowych zaleceń może zapobiec większym problemom, pomagając w rozwiązaniu i rozwiązywaniu mniejszych.

- Zawsze należy spróbować rozwiązać problemy z najwyższym poziomem priorytetu w Azure Advisor. Problemy o wysokim priorytecie są oznaczane kolorem czerwonym. Pozostawienie nierozwiązanych rekomendacji o wysokim priorytecie może prowadzić do problemów z wierszem.

- Jeśli zalecenie wydaje się mniej ważne, można je odrzucić lub odroczyć. Aby odrzucić lub odłożyć zalecenie, przejdź do kolumny **Akcja** i Zmień stan elementu.

- Nie odrzucaj zaleceń do momentu, gdy nie wiesz, dlaczego się pojawiają, i upewnij się, że nie będą mieć negatywnego wpływu na użytkownika lub użytkowników. Zawsze wybieraj **więcej informacji** , aby zobaczyć, co to jest problem. W przypadku rozwiązywania problemu, postępując zgodnie z instrukcjami w Azure Advisor, zostanie ona automatycznie usunięta z listy. Lepiej jest rozwiązywać problemy niż wielokrotne odkładanie.

- Zawsze, gdy występuje problem z pulpitem wirtualnym systemu Windows, należy najpierw sprawdzić Azure Advisor. W Azure Advisor przedstawiono wskazówki dotyczące rozwiązywania problemu lub co najmniej przekazanie do zasobu, który może pomóc.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak rozwiązać zalecenia, zobacz [temat jak rozwiązać Azure Advisor zalecenia](azure-advisor-recommendations.md).

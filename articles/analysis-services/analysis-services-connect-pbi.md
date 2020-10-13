---
title: Nawiązywanie połączenia z Azure Analysis Services przy użyciu Power BI | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z serwerem Azure Analysis Services przy użyciu Power BI. Po nawiązaniu połączenia użytkownicy mogą eksplorować dane modelu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f79ff9af1272ca908c7a5ba35fbaa1e8bb87fc50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506714"
---
# <a name="connect-with-power-bi"></a>Łączenie z usługą Power BI

Po utworzeniu serwera na platformie Azure i wdrożeniu modelu tabelarycznego użytkownicy w organizacji są gotowi do nawiązywania połączeń i rozpoczęcia eksplorowania danych. 

> [!TIP]
> Upewnij się, że korzystasz z najnowszej wersji [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Nawiązywanie połączenie w programie Power BI Desktop

1. W Power BI Desktop kliknij pozycję **Pobierz dane**  >  **Azure**  >  **Azure Analysis Services Database**.

2. W polu **serwer**wprowadź nazwę serwera. Pamiętaj, aby uwzględnić pełny adres URL; na przykład asazure://westcentralus.asazure.windows.net/advworks.

3. W **bazie danych**, jeśli znasz nazwę bazy danych modelu tabelarycznego lub perspektywę, z którą chcesz nawiązać połączenie, wklej ją w tym miejscu. W przeciwnym razie można pozostawić to pole puste i wybrać bazę danych lub perspektywę później.

4. Wybierz opcję połączenia, a następnie naciśnij przycisk **Połącz**. 

    Obsługiwane są zarówno opcje **Connect Live** , jak i **Import** . Zaleca się jednak korzystanie z połączeń na żywo, ponieważ tryb importu ma pewne ograniczenia; w szczególności może to mieć wpływ na wydajność serwera podczas importowania. Ponadto, jeśli model ma być odświeżany w usługa Power BI, ustawienie Zezwalaj na **dostęp z Power BI** ma zastosowanie tylko w przypadku wybrania opcji **Połącz na żywo**.

5. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania. 

   > [!NOTE]
   > Konta jednorazowego kodu dostępu (OTP) nie są obsługiwane. 

6. W **Nawigatorze**rozwiń serwer, a następnie wybierz model lub perspektywę, z którą chcesz się połączyć, a następnie kliknij przycisk **Połącz**. Kliknij model lub perspektywę, aby wyświetlić wszystkie obiekty dla tego widoku.

    Model zostanie otwarty w Power BI Desktop z pustym raportem w widoku raportu. Na liście Pola zostaną wyświetlone wszystkie nieukryte obiekty modelu. Stan połączenia jest wyświetlany w prawym dolnym rogu.

## <a name="connect-in-power-bi-service"></a>Połącz w Power BI (usługa)

1. Utwórz plik Power BI Desktop, który ma połączenie na żywo z modelem na serwerze.
2. W [Power BI](https://powerbi.microsoft.com)kliknij pozycję **Pobierz**  >  **pliki**danych, a następnie Znajdź i wybierz plik pbix.

## <a name="see-also"></a>Zobacz też
[Połącz z Azure Analysis Services](analysis-services-connect.md)   
[Biblioteki klienta](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)


---
title: Nawiązywanie połączenia Azure Analysis Services pomocą Power BI | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z serwerem Azure Analysis Services przy użyciu Power BI. Po na połączeniu użytkownicy mogą eksplorować dane modelu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 4/20/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5a430c5bb24032a2665ad078311dcb5137d2bb9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816036"
---
# <a name="connect-with-power-bi"></a>Łączenie z usługą Power BI

Po utworzeniu serwera na platformie Azure i wdrożeniu na nim modelu tabelaryczną użytkownicy w organizacji są gotowi do nawiązania połączenia i rozpoczęcia eksplorowania danych. 

> [!NOTE]
> W przypadku publikowania modelu Power BI Desktop na serwerze usługa Power BI upewnij się, że na serwerze Azure Analysis Services nie wybrano właściwości Case-Sensitive serwera sortowania (wartość domyślna). Właściwość Case-Sensitive server można ustawić przy użyciu SQL Server Management Studio.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Nawiązywanie połączenie w programie Power BI Desktop

1. W Power BI Desktop kliknij pozycję **Pobierz dane** Azure Azure Analysis Services  >    >  **database.**

2. W **serwer**, wprowadź nazwę serwera. Pamiętaj, aby dołączyć pełny adres URL; na przykład asazure://westcentralus.asazure.windows.net/advworks.

3. Jeśli **w obszarze** Baza danych znasz nazwę bazy danych modelu tabelarygo lub perspektywę, z którą chcesz nawiązać połączenie, wklej ją tutaj. W przeciwnym razie możesz pozostawić to pole puste i wybrać bazę danych lub perspektywę później.

4. Wybierz opcję połączenia, a następnie naciśnij przycisk **Połącz.** 

    Obsługiwane **są zarówno opcje Połącz na** **żywo, jak** i Import. Zalecamy jednak używanie połączeń na żywo, ponieważ tryb importu ma pewne ograniczenia. W szczególności podczas importowania może to mieć wpływ na wydajność serwera. Ponadto, jeśli model ma zostać odświeżony w usługa Power BI,  ustawienie Zezwalaj na dostęp z Power BI ma zastosowanie tylko w przypadku wybrania opcji Połącz na **żywo.**

5. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania. 

   > [!NOTE]
   > Konta z jednorazowym kodem dostępu (OTP) nie są obsługiwane. 

6. W **Nawigatorze** rozwiń serwer, wybierz model lub perspektywę, z którą chcesz nawiązać połączenie, a następnie kliknij przycisk **Połącz**. Kliknij model lub perspektywę, aby wyświetlić wszystkie obiekty dla tego widoku.

    Model zostanie otwarty w Power BI Desktop z pustym raportem w widoku Raport. Na liście Pola zostaną wyświetlone wszystkie nieukryte obiekty modelu. Stan połączenia jest wyświetlany w prawym dolnym rogu.

## <a name="connect-in-power-bi-service"></a>Łączenie w Power BI (usługa)

1. Utwórz plik Power BI Desktop, który ma połączenie na żywo z modelem na serwerze.
2. W [Power BI](https://powerbi.microsoft.com)kliknij pozycję **Pobierz pliki** danych, a następnie znajdź i wybierz plik  >  pbix.

## <a name="see-also"></a>Zobacz też
[Nawiązywanie połączenia z Azure Analysis Services](analysis-services-connect.md)   
[Biblioteki klienta](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)

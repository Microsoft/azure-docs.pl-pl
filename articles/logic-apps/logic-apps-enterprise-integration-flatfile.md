---
title: Kodowanie lub dekodowanie plików prostych
description: Koduj lub Dekoduj pliki płaskie dla integracji przedsiębiorstwa z Azure Logic Apps i Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77152656"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Koduj lub Dekoduj pliki płaskie za pomocą Azure Logic Apps i Pakiet integracyjny dla przedsiębiorstw

Możesz chcieć zakodować zawartość XML przed wysłaniem jej do partnera biznesowego w scenariuszu biznesowym (B2B). W aplikacji logiki można w tym celu użyć łącznika prostego pliku kodowania. Utworzona aplikacja logiki może pobrać zawartość XML z różnych źródeł, w tym z wyzwalacza żądania HTTP, z innej aplikacji lub nawet z jednego z wielu [łączników](../connectors/apis-list.md). Aby uzyskać więcej informacji na temat usługi Logic Apps, zobacz [dokumentację usługi Logic Apps](logic-apps-overview.md "Dowiedz się więcej o usłudze Logic Apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Tworzenie łącznika prostego kodowania plików
Wykonaj następujące kroki, aby dodać łącznik prostego kodowania plików do aplikacji logiki.

1. Utwórz aplikację logiki i [Połącz ją z kontem integracji](logic-apps-enterprise-integration-accounts.md "Dowiedz się, jak połączyć konto integracji z aplikacją logiki"). To konto zawiera schemat, który będzie używany do kodowania danych XML.  

1. W Projektancie aplikacji logiki Dodaj wyzwalacz **po odebraniu żądania HTTP** do aplikacji logiki.

1. Dodaj akcję kodowania pliku prostego w następujący sposób:

   a. Wybierz znak **Plus** .

   b. Wybierz łącze **Dodaj akcję** (pojawia się po wybraniu znaku plusa).

   c. W polu wyszukiwania wprowadź wartość *Flat* , aby odfiltrować wszystkie akcje do tej, która ma zostać użyta.

   d. Wybierz z listy opcję **kodowania pliku prostego** .   

      ![Zrzut ekranu przedstawiający opcję kodowania pliku prostego](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. W oknie dialogowym **kodowanie pliku prostego** zaznacz pole tekstowe **zawartość** .  

   ![Zrzut ekranu przedstawiający pole tekstowe zawartości](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Wybierz tag treści jako zawartość, którą chcesz zakodować. Tag treści wypełni pole zawartość.     

   ![Zrzut ekranu przedstawiający tag treści](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Zaznacz pole listy **Nazwa schematu** i wybierz schemat, którego chcesz użyć do kodowania zawartości wejściowej.    

   ![Zrzut ekranu przedstawiający pole listy Nazwa schematu](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Zapisz pracę.

   ![Zrzut ekranu przedstawiający ikonę zapisywania](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

W tym momencie zakończysz Konfigurowanie łącznika prostego pliku kodowania. W świecie rzeczywistym można przechowywać dane zakodowane w aplikacji biznesowych, na przykład w usłudze Salesforce. Możesz też wysłać te dane zakodowane do partnera handlowego. Możesz łatwo dodać akcję w celu wysłania danych wyjściowych akcji kodowania do usługi Salesforce lub do partnera handlowego przy użyciu dowolnego z oferowanych łączników.

Teraz można testować łącznik, wysyłając żądanie do punktu końcowego HTTP i uwzględniając zawartość XML w treści żądania.  

## <a name="create-the-flat-file-decoding-connector"></a>Utwórz łącznik prostego dekodowania plików

> [!NOTE]
> Aby wykonać te czynności, musisz mieć plik schematu, który został już przekazany do konta integracji.

1. W Projektancie aplikacji logiki Dodaj wyzwalacz **po odebraniu żądania HTTP** do aplikacji logiki.

1. Dodaj akcję dekodowania prostego pliku w następujący sposób:

   a. Wybierz znak **Plus** .

   b. Wybierz łącze **Dodaj akcję** (pojawia się po wybraniu znaku plusa).

   c. W polu wyszukiwania wprowadź wartość *Flat* , aby odfiltrować wszystkie akcje do tej, która ma zostać użyta.

   d. Wybierz opcję **prostego dekodowania pliku** z listy.   

      ![Zrzut ekranu przedstawiający opcję prostego dekodowania pliku](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Wybierz kontrolkę **zawartość** . Spowoduje to utworzenie listy zawartości z wcześniejszych kroków, których można użyć jako zawartości do zdekodowania. Zwróć uwagę, że *treść* z przychodzącego żądania HTTP jest dostępna do użycia jako zawartość do zdekodowania. Możesz również wprowadzić zawartość, która ma zostać zdekodowana bezpośrednio do kontrolki **zawartość** .     

1. Wybierz tag *treści* . Zwróć uwagę, że tag treści znajduje się teraz w kontrolce **zawartość** .

1. Wybierz nazwę schematu, którego chcesz użyć do zdekodowania zawartości. Poniższy zrzut ekranu pokazuje, że *OrderFile* jest wybraną nazwą schematu. Ta nazwa schematu została wcześniej przekazana do konta integracji.

   ![Zrzut ekranu przedstawiający okno dialogowe prostego dekodowania plików](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Zapisz pracę.  

   ![Zrzut ekranu przedstawiający ikonę zapisywania](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

W tym momencie skończysz skonfigurować łącznik prostego dekodowania plików. W świecie rzeczywistym można przechowywać zdekodowane dane w aplikacji branżowej, takiej jak Salesforce. Możesz łatwo dodać akcję w celu wysłania danych wyjściowych akcji dekodowania do usługi Salesforce.

Teraz można testować łącznik, wysyłając żądanie do punktu końcowego HTTP i uwzględniając zawartość XML, która ma zostać zdekodowana w treści żądania.  

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw").  


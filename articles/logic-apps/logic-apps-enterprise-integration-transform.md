---
title: Przekształć XML między formatami
description: Twórz przekształcenia lub mapy, które konwertują XML między formatami w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 038c1d4c0f0b5ffd7b9aabea2de32e3a44e3b221
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654136"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Tworzenie map, które przekształcają kod XML między formatami w usłudze Azure Logic Apps z Pakietem integracyjnym dla przedsiębiorstw

Łącznik przekształceń integracji przedsiębiorstwa konwertuje dane z jednego formatu na inny. Na przykład możesz mieć wiadomość przychodzącą zawierającą bieżącą datę a formacie RokMiesiącDzień. Przy użyciu przekształcenia możesz zmienić format daty na MiesiącDzieńRok.

## <a name="what-does-a-transform-do"></a>Co to jest transformacja?
Transformacja, która jest również znana jako mapa, składa się ze źródłowego schematu XML (dane wejściowe) i docelowego schematu XML (dane wyjściowe). Można użyć różnych wbudowanych funkcji, które ułatwiają manipulowanie danymi, w tym manipulowanie ciągami, przypisania warunkowe, wyrażenia arytmetyczne, Programy formatujące daty i godziny, a nawet konstrukcje pętli.

## <a name="how-to-create-a-transform"></a>Jak utworzyć transformację?
Można utworzyć transformację/mapę przy użyciu [zestawu SDK](https://aka.ms/vsmapsandschemas)programu Visual Studio integracja dla przedsiębiorstw. Po zakończeniu tworzenia i testowania transformacji Przekształć ją na konto integracji. 

## <a name="how-to-use-a-transform"></a>Jak używać transformacji
Po przekazaniu przekształcenia/mapy do konta integracji można użyć go do utworzenia aplikacji logiki. Aplikacja logiki uruchamia przekształcenia przy każdym wyzwoleniu aplikacji logiki (i ma zawartość wejściową, która musi zostać przekształcona).

**Poniżej przedstawiono kroki, które należy wykonać, aby użyć przekształcenia**:

### <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie konta integracji i dodawanie do niego mapy  

Teraz, po pobraniu wymagań wstępnych, należy utworzyć aplikację logiki:  

1. Utwórz aplikację logiki i [Połącz ją z kontem integracji](./logic-apps-enterprise-integration-create-integration-account.md "Dowiedz się, jak połączyć konto integracji z aplikacją logiki") zawierającym mapę.
2. Dodawanie wyzwalacza **żądania** do aplikacji logiki  
   ![Zrzut ekranu przedstawiający listę rozwijaną "Pokaż zarządzane interfejsy API firmy Microsoft" z wybranym wyzwalaczem żądania. Lista rozwijana znajduje się w aplikacji logiki utworzonej przy użyciu zestawu SDK integracji Visual Studio Enterprise.](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Dodaj akcję **Przekształć XML** , wybierając najpierw pozycję **Dodaj akcję** .   
   ![Zrzut ekranu przedstawiający przycisk "Dodaj akcję" wybrany na ekranie wyzwalacza żądania.](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Wprowadź *transformację* wyrazu w polu wyszukiwania, aby odfiltrować wszystkie akcje, które mają być używane.  
   ![Zrzut ekranu przedstawiający sposób wyszukiwania akcji Przekształć XML na liście rozwijanej "Pokaż interfejsy API zarządzane przez firmę Microsoft", aby można ją było dodać do wyzwalacza żądania.](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Wybierz akcję **Przekształć XML**   
6. Dodaj przekształcenie **zawartość** XML. Możesz użyć dowolnych danych XML otrzymywanych w żądaniu HTTP jako **zawartości**. W tym przykładzie wybierz treść żądania HTTP, które wyzwoliło aplikację logiki.

   > [!NOTE]
   > Upewnij się, że zawartość **pliku XML transformacji** to XML. Jeśli zawartość nie jest w formacie XML lub jest zakodowana algorytmem Base64, należy określić wyrażenie, które przetwarza zawartość. Na przykład można użyć [funkcji](logic-apps-workflow-definition-language.md#functions), takich jak ```@base64ToBinary``` dekodowanie zawartości lub ```@xml``` do przetwarzania zawartości w formacie XML.
 

7. Wybierz nazwę **mapy** , która ma zostać użyta do przeprowadzenia transformacji. Mapa musi już znajdować się na koncie integracji. W poprzednim kroku masz już dostęp do aplikacji logiki do konta integracji, które zawiera Twoje mapowanie.      
   ![Zrzut ekranu przedstawiający pola zawartość i mapa na ekranie Przekształć XML dla wyzwalacza żądania.](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Zapisz wyniki dotychczasowej pracy  
    ![Zrzut ekranu przedstawiający przycisk Zapisz w projektancie Logic Apps.](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

W tym momencie skończysz Konfigurowanie mapy. W świecie rzeczywistym można przechowywać przekształcone dane w aplikacji LOB, takiej jak SalesForce. Możesz łatwo wykonać akcję wysłania danych wyjściowych transformacji do usługi Salesforce. 

Możesz teraz przetestować transformację, wysyłając żądanie do punktu końcowego HTTP.  


## <a name="features-and-use-cases"></a>Funkcje i przypadki użycia
* Transformacja utworzona w mapie może być prosta, taka jak kopiowanie nazwy i adresu z jednego dokumentu do drugiego. Można też tworzyć bardziej złożone przekształcenia przy użyciu wbudowanych operacji na mapie.  
* Wiele operacji lub funkcji mapy jest łatwo dostępnych, takich jak ciągi, funkcje daty i godziny itd.  
* Można wykonać bezpośrednią kopię danych między schematami. W odwzorze zawartym w zestawie SDK jest to proste, jak rysowanie linii łączącej elementy w schemacie źródłowym z odpowiadającymi im odpowiednikami w schemacie docelowym.  
* Podczas tworzenia mapy można wyświetlić graficzną reprezentację mapy, która zawiera wszystkie utworzone relacje i linki.
* Użyj funkcji mapy testów, aby dodać przykładowy komunikat XML. Za pomocą prostego kliknięcia można przetestować utworzoną mapę i wyświetlić wygenerowane dane wyjściowe.  
* Przekaż istniejące mapy  
* Obejmuje obsługę formatu XML.

## <a name="advanced-features"></a>Funkcje zaawansowane

### <a name="reference-assembly-or-custom-code-from-maps"></a>Zestaw referencyjny lub kod niestandardowy z map 
Akcja przekształcenia obsługuje również mapy lub przekształcenia z odwołaniem do zestawu zewnętrznego. Ta funkcja umożliwia wywołania niestandardowego kodu platformy .NET bezpośrednio z map XSLT. Poniżej przedstawiono wymagania wstępne dotyczące używania zestawu w usłudze Maps.

* Mapa i zestaw, do których odwołuje się mapowanie, muszą zostać [przekazane do konta integracji](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Mapowanie i zestaw są wymagane do przekazania w określonej kolejności. Należy przekazać zestaw przed przekazaniem mapy, która odwołuje się do zestawu.

* Mapa musi mieć również te atrybuty i sekcję CDATA, która zawiera wywołanie kodu zestawu:

    * **Nazwa** jest nazwą niestandardowego zestawu.
    * **przestrzeń nazw** jest przestrzenią nazw w zestawie, która zawiera kod niestandardowy.

  Ten przykład pokazuje mapę, która odwołuje się do zestawu o nazwie "XslUtilitiesLib" i wywołuje `circumreference` metodę z zestawu.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
   <circles>
    <xsl:for-each select="circle">
      <circle>
        <xsl:copy-of select="node()"/>
          <circumference>
            <xsl:value-of select="user:circumference(radius)"/>
          </circumference>
      </circle>
    </xsl:for-each>
   </circles>
  </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Znacznik kolejności bajtów
Domyślnie odpowiedź z transformacji zaczyna się od znacznika kolejności bajtów (BOM). Możesz uzyskać dostęp do tej funkcji tylko podczas pracy w edytorze widoku kodu. Aby wyłączyć tę funkcję, określ `disableByteOrderMark` `transformOptions` Właściwość:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Dowiedz się więcej
* [Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw")  
* [Dowiedz się więcej o mapach](../logic-apps/logic-apps-enterprise-integration-maps.md "Dowiedz się więcej na temat map integracji przedsiębiorstwa")  
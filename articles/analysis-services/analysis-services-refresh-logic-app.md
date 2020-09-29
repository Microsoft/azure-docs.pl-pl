---
title: Odświeżanie za pomocą Logic Apps dla modeli Azure Analysis Services | Microsoft Docs
description: W tym artykule opisano sposób kodu odświeżania asynchronicznego dla Azure Analysis Services przy użyciu Azure Logic Apps.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.custom: references_regions
ms.openlocfilehash: fd5c4043d417a99c7ffa57534fd7808f1710190a
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448586"
---
# <a name="refresh-with-logic-apps"></a>Odświeżanie za pomocą usługi Logic Apps

Za pomocą Logic Apps i wywołań REST można wykonywać automatyczne operacje odświeżania danych w modelach tabelarycznych usługi Azure Analysis, włącznie z synchronizacją replik tylko do odczytu na potrzeby skalowania zapytań.

Aby dowiedzieć się więcej o korzystaniu z interfejsów API REST z Azure Analysis Services, zobacz [odświeżanie asynchroniczne za pomocą interfejsu API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Authentication

Wszystkie wywołania muszą zostać uwierzytelnione z prawidłowym tokenem Azure Active Directory (OAuth 2).  W przykładach w tym artykule zostanie użyta nazwa główna usługi (SPN) do uwierzytelniania w Azure Analysis Services. Aby dowiedzieć się więcej, zobacz [Tworzenie jednostki usługi przy użyciu Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Projektowanie aplikacji logiki

> [!IMPORTANT]
> W poniższych przykładach założono, że Zapora Azure Analysis Services jest wyłączona. Jeśli Zapora jest włączona, publiczny adres IP inicjatora żądania musi być listy dozwolonych w zaporze Azure Analysis Services. Aby dowiedzieć się więcej o Azure Logic Apps zakresach adresów IP na region, zobacz [Informacje o limitach i konfiguracji Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Wymagania wstępne

#### <a name="create-a-service-principal-spn"></a>Tworzenie nazwy głównej usługi (SPN)

Aby dowiedzieć się więcej na temat tworzenia nazwy głównej usługi, zobacz [Tworzenie jednostki usługi przy użyciu Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurowanie uprawnień w Azure Analysis Services
 
Utworzona jednostka usługi musi mieć uprawnienia administratora serwera na serwerze. Aby dowiedzieć się więcej, zobacz [Dodawanie nazwy głównej usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Konfigurowanie aplikacji logiki

W tym przykładzie aplikacja logiki została zaprojektowana tak, aby wyzwalać po odebraniu żądania HTTP. Spowoduje to włączenie narzędzia aranżacji, takiego jak Azure Data Factory, do wyzwolenia Azure Analysis Services odświeżenia modelu.

Po utworzeniu aplikacji logiki:

1. W Projektancie aplikacji logiki wybierz pierwszą akcję, jak w **przypadku odebrania żądania HTTP**.

   ![Dodaj działanie odebrane HTTP](./media/analysis-services-async-refresh-logic-app/1.png)

Po zapisaniu aplikacji logiki ten krok zostanie wypełniony przy użyciu adresu URL POST protokołu HTTP.

2. Dodaj nowy krok i wyszukaj ciąg **http**.  

   ![Zrzut ekranu przedstawiający sekcję "Wybieranie akcji" z wybranym kafelkiem "HTTP".](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Zrzut ekranu okna "HTTP" z wybranym kafelkiem "HTTP-HTTP".](./media/analysis-services-async-refresh-logic-app/10.png)

3. Wybierz pozycję **http** , aby dodać tę akcję.

   ![Dodawanie działania HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Skonfiguruj działanie HTTP w następujący sposób:

|Właściwość  |Wartość  |
|---------|---------|
|**Metoda**     |POST         |
|**URI**     | https://*serwera*/Servers/*AAS nazwa serwera*/models/*Nazwa bazy danych*/refreshes <br /> <br /> Na przykład: https: \/ /westus.asazure.Windows.NET/Servers/MyServer/models/AdventureWorks/refreshes|
|**Nagłówki**     |   Content-Type, Application/JSON <br /> <br />  ![Nagłówki](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Treść**     |   Aby dowiedzieć się więcej na temat tworzenia treści żądania, zobacz [odświeżanie asynchroniczne za pomocą interfejsu API REST — post/refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Authentication**     |Active Directory OAuth         |
|**Dzierżawa**     |Wypełnij Azure Active Directory TenantId         |
|**Grupy odbiorców**     |https://*. asazure. Windows. NET         |
|**Identyfikator klienta**     |Wprowadź nazwę główną usługi ClientID         |
|**Typ poświadczeń**     |Wpis tajny         |
|**Wpis tajny**     |Wprowadź klucz tajny nazwy głównej usługi         |

Przykład:

![Ukończono działanie HTTP](./media/analysis-services-async-refresh-logic-app/7.png)

Teraz Przetestuj aplikację logiki.  W Projektancie aplikacji logiki kliknij pozycję **Uruchom**.

![Testowanie aplikacji logiki](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Korzystanie z aplikacji logiki przy użyciu Azure Data Factory

Po zapisaniu aplikacji logiki Sprawdź, czy **Odebrano żądanie HTTP** , a następnie skopiuj teraz wygenerowany **adres URL post protokołu HTTP** .  Jest to adres URL, który może być używany przez Azure Data Factory, aby wywołać wywołanie asynchroniczne w celu wyzwolenia aplikacji logiki.

Oto przykład Azure Data Factory działania sieci Web, które wykonuje tę akcję.

![Data Factory aktywność sieci Web](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Korzystanie z samodzielnej aplikacji logiki

Jeśli nie planujesz używania narzędzia Orchestration, takiego jak Data Factory, aby wyzwolić odświeżanie modelu, można ustawić aplikację logiki, aby wyzwolić odświeżanie na podstawie harmonogramu.

Korzystając z powyższego przykładu, Usuń pierwsze działanie i zastąp je działaniem **harmonogramu** .

![Zrzut ekranu, na którym zostanie wyświetlona strona "Logic Apps" z wybranym kafelkiem "Schedule".](./media/analysis-services-async-refresh-logic-app/12.png)

![Zrzut ekranu przedstawiający stronę "wyzwalacze".](./media/analysis-services-async-refresh-logic-app/13.png)

W tym przykładzie zostanie użyty **cykl**.

Po dodaniu działania Skonfiguruj interwał i częstotliwość, a następnie Dodaj nowy parametr i wybierz **w tych godzinach**.

![Zrzut ekranu przedstawiający sekcję "cykl" z wybranym parametrem "w tych godzinach".](./media/analysis-services-async-refresh-logic-app/16.png)

Wybierz odpowiednie godziny.

![Planowanie działania](./media/analysis-services-async-refresh-logic-app/15.png)

Zapisz aplikację logiki.

## <a name="next-steps"></a>Następne kroki

[Samples](analysis-services-samples.md)  
[Interfejs API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
